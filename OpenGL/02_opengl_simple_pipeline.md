

## OpenGL Simple PipeLine (1)

![pipeline](https://people.eecs.ku.edu/~jrmiller/Courses/672/InClass/MaterialsFromElsewhere/SimplifiedGraphicsPipeline_FromSuperBible.png)

### 파이프 라인

: 파이프라인의 각 단계를 스테이지라고 한다. 스테이지는 종류별로 고정함수와 셰이더로 나뉜다. 셰이더는 프로그래밍 가능한 스테이지로 네모박스에 해당한다. 고정함수는 설정 변경만 가능하다. 이번 포스팅에는 rasterization 이전의 과정을 살펴 볼 것이다.

1. vertex fetch, vertex shader<br>

    vertex shader로 입력에 대해 in 저장 지시어를 사용하면 vertex fetch 스테이지에 의해 그 내용이 변수에 자동적으로 채워 진다. 이 변수를 버텍스 속성이라 한다. 애플리캐이션에서 glVertexAttrib*()라는 함수들을 통해 내용을 채울 수 있다.<br>
 
    ![](images/02_opengl_simple_pipeline/0.png)

    ![](images/02_opengl_simple_pipeline/1.png)

    다른 스테이지로 데이터를 전달이 가능하다. 위의 out지시어를 통한 컬러값을 출력하고 있다. 이 데이터를 fragment shader에서 in 지시어를 사용해여 데이터를 전달 받을 수 있다.

    ![](images/02_opengl_simple_pipeline/2.png)
 

2. tesellation

     tesellation을 간단하게 말해 폴리곤을 더 작은 폴리곤으로 분할 하는 쪼게는 기능이라고 생각하면 된다. OpenGL 파이프 라인에서  tesellation은 tesellation control shader, tesellation engine, tesellation evaluation shader 세 부분으로 구성 된다.

    - tesllation control shader

        tesellation engine에 보낼 tesellation 레벨을 결정하는 일과, tesellation이 수행된 다음에 tesellation evaluation shader에 보낼 데이터를 생성하는 일이다. tesellation은 control point에 의해 점, 선, 삼각형을 분할 한다.

        ![](images/02_opengl_simple_pipeline/3.png)

    - tesellation engine

        패치로 표현되는 고차 서피스를 점, 선, 삼각형 같은 작은 프리미티브로 분할하는 역할을 수행한다.

    - tesellation evaluation shader

        tesellator에 의해 생성된 각 버텍스에 대해 한번씩 호출을 수행한다. 현재 무게중심에 버텍스를 위치시킨다.
        
        ![](images/02_opengl_simple_pipeline/4.png)

    - 결과

        ![](images/02_opengl_simple_pipeline/5.png)

3. geometry shader

    프리미티브당 한번 수행되며, 수행되는 프리미티브를 구성하는 모든 버텍스에 대한 데이터에 접근 할 수 있다. 그리고 파이프라인 내 데이터 흐름의 양을 증가시키거나 감소시킬 수 있는 유일한 스테이지이다.

    ![](images/02_opengl_simple_pipeline/6.png)

    ![](images/02_opengl_simple_pipeline/7.png)

4. 클리핑

    버텍스는 버텍스 쉐이더를 떠날 때 클립 공간이라는 정규화된 디바이스 공간에 있게 된다. x, y 차원에서는 -1 ~ 1, z차원에서는 0 ~ 1의 볼륨 영역이다. 이때 버텍스가 클립공간 밖에 있으면 버려지고 클립공간에 걸쳐 있으면 버텍스를 짤라 새로운 버텍스를 만드는 처리를 하는데 이를 클리핑이라고 한다.

5. 뷰포트 변환

    정규화된 디바이스 좌표에서 윈도우 좌표[(0, 0) ~ (w - 1, h - 1)]로 이동시킨다.

6. 컬링

    glEnable함수를 사용하여 컬링을 수행할 수 있다. glCullFace로 backspace culling을 수행할 것인지 frontspace culling수행할 것인지 결정 할 수 있다.

- 코드

<pre>
<code>
int main()
{
	glfwSetErrorCallback(show_glfw_error);

	if (!glfwInit()) {
		exit(-1);
	}

	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
	glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);

	GLFWwindow* window = glfwCreateWindow(
		800, // width
		600, // height
		"OpenGL Example",
		NULL, NULL);

	if (!window)
	{
		glfwTerminate();
		exit(-1);
	}

	glfwMakeContextCurrent(window);
	glfwSetWindowSizeCallback(window, window_resized);
	glfwSetKeyCallback(window, key_pressed);

	glfwSwapInterval(1);

	glewExperimental = GL_TRUE;
	GLenum err = glewInit();
	if (err != GLEW_OK) {
		glfwTerminate();
		exit(-1);
	}

	std::string vsSource, fsSource, tcsSource, tesSource, gsSource;
	read_shader("vertex_shader.glsl", vsSource);
	read_shader("fragment_shader.glsl", fsSource);
	read_shader("tesellation_controll_shader.glsl", tcsSource);
	read_shader("tessellation_evaluation_shader.glsl", tesSource);
	read_shader("geometry_shader.glsl", gsSource);

	const char* vsPointer = vsSource.c_str();
	const char* fsPointer = fsSource.c_str();
	const char* tcsPointer = tcsSource.c_str();
	const char* tesPointer = tesSource.c_str();
	const char* gsPointer = gsSource.c_str();

	GLuint vs = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vs, 1, &vsPointer, nullptr);
	glCompileShader(vs);

	GLuint fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fs, 1, &fsPointer, nullptr);
	glCompileShader(fs);

	GLuint tcs = glCreateShader(GL_TESS_CONTROL_SHADER);
	glShaderSource(tcs, 1, &tcsPointer, nullptr);
	glCompileShader(tcs);

	GLuint tes = glCreateShader(GL_TESS_EVALUATION_SHADER);
	glShaderSource(tes, 1, &tesPointer, nullptr);
	glCompileShader(tes);

	GLuint gs = glCreateShader(GL_GEOMETRY_SHADER);
	glShaderSource(gs, 1, &gsPointer, nullptr);
	glCompileShader(gs);

	GLuint program = glCreateProgram();
	glAttachShader(program, vs);
	glAttachShader(program, tcs);
	glAttachShader(program, tes);
	glAttachShader(program, gs);
	glAttachShader(program, fs);
	glLinkProgram(program);

	glDetachShader(program, vs);
	glDetachShader(program, tcs);
	glDetachShader(program, tes);
	glDetachShader(program, gs);
	glDetachShader(program, fs);

	glDeleteShader(vs);
	glDeleteShader(tcs);
	glDeleteShader(tes);
	glDeleteShader(gs);
	glDeleteShader(fs);


	GLuint vertexArrayObject;
	glGenVertexArrays(1, &vertexArrayObject);
	glBindVertexArray(vertexArrayObject);

	glClearColor(0, 0, 1, 1);

	glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);

	while (!glfwWindowShouldClose(window)) {

		glClearColor(0, 0, 1, 1);

		glClear(GL_COLOR_BUFFER_BIT);

		glUseProgram(program);

		GLfloat offset[] = { 0.0f, 0.0f, 0.0f, 0.0f };
		GLfloat color[] = { 1.0f, 1.0f, 1.0f, 1.0f };

		glVertexAttrib4fv(0, offset);
		glVertexAttrib4fv(1, color);

		glPointSize(10);

		glDrawArrays(GL_PATCHES, 0, 3);

		glfwSwapBuffers(window);
		glfwPollEvents();
	}

	glDeleteVertexArrays(1, &vertexArrayObject);
	glDeleteProgram(program);

	glfwDestroyWindow(window);

	glfwTerminate();
	return 0;
}
</code>
</pre>
