![pipeline](https://people.eecs.ku.edu/~jrmiller/Courses/672/InClass/MaterialsFromElsewhere/SimplifiedGraphicsPipeline_FromSuperBible.png)

이번포스팅에는 geometry shader 이후의 각 스테이지에 대해서 살펴보겠습니다.

## Rasterization

: fragment들이 primitive에 의해 채워지는지 결정하는 작업. OpenGL의 경우 삼각형에 대한 바운딩 박스를 구하고 모든 프래그먼트가 삼각형의 안쪽에 있는지 바깥쪽에 있는지 검사한다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d6/Pixels_covered_by_a_triangle.png/200px-Pixels_covered_by_a_triangle.png)

## Fragment Shader

: 각 fragment의 색상을 결정하여 나중에 프레임 버퍼로 보냄. 삼각형이 많은 양의 fragment를 생성하기 때문에 작업량이 폭발적으로 증가한다. 라이팅, 재질 등의 역할을 할 수 있다. 또한 fragment shader는 다른 스테이지와 달리 입력값을 프리미티브에 따라 보간한다.

![](images/02_opengl_simple_pipeline/8.png)

![](images/02_opengl_simple_pipeline/9.png)

![](images/02_opengl_simple_pipeline/10.png)

## Frame Buffer

: 화면에 나타내는 영역을 나타낸다. fragment shader에 의해 쓰일 위치 및 포맷 등에 대한 상태 정보를 Frame Buffer Instance에 저장한다. frame buffer instance에 저장되지 않는 픽셀 오퍼레이션도 있다.

## Compute Shader

: OpenGL은 그래픽스 관련 스테이지와 별개로 동작하는 파이프라인으로 Compute Shader를 제공한다. 자세한 내용은 나중에 자세히 다루겠습니다.