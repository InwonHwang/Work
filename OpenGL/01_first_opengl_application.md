# Shader
: GPU 스테이지에서 실행되도록 디자인된 유저 정의 프로그램. shader는 렌더링 파이프라인의 프로그래밍 가능한 스테이지에서 실행 됩니다.

1. vertex shader : 입력 버텍스 스트림에서 버텍스를 받아 출력용 버텍스 스트림에 버텍스를 생성한다. 입력용으로 들어온 버텍스와 출력용 버텍스는 1대1 매핑된다.

2. fragment shader : 래스터라이즈 결과로 생성되는 값들의 집합을 fragment라고 한다. fragment shader는 생성된 fragment를 픽셀에 표현한다.


# 예제

: 렌더링 파이프라인은 적어도 하나의 vertex shader(또는 compute shader)로 이루어지고 화면에 출력을 위해서는 fragment shader가 필요합니다.

1. vertex shader와 fragment shader를 생성합니다. 파일 확장자명은 자유롭게 해도 상관없습니다.

    ![](images/01_first_opengl_application/0.png)

    vertex_shader.vs.glsl

    ![](images/01_first_opengl_application/2.png)

    fragment_shader.vs.glsl

    ![](images/01_first_opengl_application/1.png)

2. shader 소스 코드는 shader 객체로 컴파일 되고 하나 이상의 shader 객체들이 프로그램 객체로 링크 됩니다.

<pre>   
<code>
    #include < GL/glew.h >
    #include < GLFW/glfw3.h >
    #include < iostream >
    #include < fstream >
    #include < sstream >

    void show_glfw_error(int error, const char* description) {
        std::cerr << "Error: " << description << '\n';
    }

    void window_resized(GLFWwindow* window, int width, int height) {
        std::cout << "Window resized, new window size: " << width << " x " << height << '\n';

        glClearColor(0, 0, 1, 1);
        glClear(GL_COLOR_BUFFER_BIT);
        glfwSwapBuffers(window);
    }

    void key_pressed(GLFWwindow* window, int key, int scancode, int action, int mods) {
        if (key == 'Q' && action == GLFW_PRESS) {
            glfwTerminate();
            exit(0);
        }
    }

    void read_shader(const std::string& fileName, std::string& shaderCode)
    {
        std::ifstream stream(fileName.c_str(), std::ios::in);

        if (!stream.is_open()) return;
        
        std::stringstream sstr;
        sstr << stream.rdbuf();

        shaderCode = sstr.str();

        stream.close();
    }

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
        
        std::string vertexShaderSource, fragmentShaderSource;
        read_shader("vertex_shader.vs.glsl", vertexShaderSource);
        read_shader("fragment_shader.fs.glsl", fragmentShaderSource);

        const char* vsPointer = vertexShaderSource.c_str();
        const char* fsPointer = fragmentShaderSource.c_str();

        GLuint vertexShader = glCreateShader(GL_VERTEX_SHADER);
        glShaderSource(vertexShader, 1, &vsPointer, nullptr);
        glCompileShader(vertexShader);

        GLuint fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
        glShaderSource(fragmentShader, 1, &fsPointer, nullptr);
        glCompileShader(fragmentShader);

        GLuint program = glCreateProgram();
        glAttachShader(program, vertexShader);
        glAttachShader(program, fragmentShader);
        glLinkProgram(program);

        glDetachShader(program, vertexShader);
        glDetachShader(program, fragmentShader);

        glDeleteShader(vertexShader);
        glDeleteShader(fragmentShader);

        
        GLuint vertexArrayObject;
        glGenVertexArrays(1, &vertexArrayObject);
        glBindVertexArray(vertexArrayObject);
        
        glClearColor(0, 0, 1, 1);
            

        while (!glfwWindowShouldClose(window)) {

            glClearColor(0, 0, 1, 1);

            glClear(GL_COLOR_BUFFER_BIT);

            glUseProgram(program);
            glDrawArrays(GL_TRIANGLES, 0, 3);		

            glfwSwapBuffers(window);
            glfwPollEvents();
        }

        glDeleteVertexArrays(1, &vertexArrayObject);
        glDeleteProgram(program);
        glDeleteVertexArrays(1, &vertexArrayObject);


        glfwDestroyWindow(window);

        glfwTerminate();
        return 0;
    }

</code>
</pre>

## 함수

|함수명| 설명|
|---|---|
|glCreateShader|빈 쉐이더 객체 생성|
|glShaderSource|shader 소스 코드를 shader 객체로 전달해서 복사본을 유지|
|glCompileShader|쉐이더 객체에 포함된 소스 코드 컴파일|
|glCreateProgram|shader객체를 붙일 프로그램 객체 생성|
|glAttachShader|shader 객체를 프로그램에 붙인다. program에 shader의 참조 전달|
|glLinkProgram|프로그램 객체에 어태치된 모든 shader 객체 링크|
|glDeleteShader|shader 객체 삭제.|
|glGenVertexArrays|버텍스 배열 객체 생성.|
|glBindVertexArray|버텍스 배영 객체를 바인딩 함.|
|glDeleteProgram|프로그램 객체 삭제.|
|glUseProgram|매개변수로 전달된 program을 렌더링 할 때 사용.|
|glDrawArrays|버텍스들을 OpenGL 파이프라인에 보낸다.|
