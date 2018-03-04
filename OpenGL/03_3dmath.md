# 3D 그래픽스를 위한 초간단 수학

## 벡터

: 크기와 방향을 나타내는 물리량

1. 벡터의 표현

: 원점으로 x, y, z방향으로 얼마나 떨어져있는지 표현함

>
    v = (1, 1, 2)          
    v = (10, 0, 0, 1)    // 4번째 원소는 w를 나타냄.

    w값은 동차좌표로 만들기 위해 추가되었다.
>

2. 벡터의 크기

>
    v = (1, 1, 2)
    v의 크기는 sqrt( 1*1 + 1*1 + 2*2 )
>

3. 단위 벡터

: 크기가 1인 벡터를 의미. 단위벡터가 아닌 벡터를 크기가 1인 벡터로 만드는 과정을 nomalization 이라고 한다.

4. 내적(Dot)

: 두 벡터의 각도를 구함.

>
    v0 = (x0, y0, z0)
    v1 = (x1, y1, z1)

    radian = x0 * x1 + y0 * x1 + z0 * z1
    degree = acos(radian)
>

5. 외적(Cross)

: 두 벡터에 수직인 벡터를 구함.

>
    v0 = (x0, y0, z0)
    v1 = (x1, y1, z1)

    v = (y0 * z1 - y1 * z0) + 
        (z0 * x1 - x0 * z1) +
        (x0 * y1 - y0 * x1)
>

6. 반사(Reflect)

![](data:image/jpeg;base64,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)

Rreflect = Rin - 2*(N, Rin의 내적) * N

## 행렬

: 복잡한 관계가 있는 변수들의 여러공식을 매우 단순히 계산할 수 있는수학적 도구이다.

>
    m = { m00, m10, m20, m30,
          m01, m11, m21, m31,
          m02, m12, m22, m32,
          m03, m13, m23, m33 } 

    x축 = (m00, m01, m02)
    y축 = (m10, m11, m12)
    z축 = (m20, m21, m22)

    위치 = (m30, m31, m32)
>

1. 단위 행렬

>
    m = { 1, 0, 0, 0,
          0, 1, 0, 0,
          0, 0, 1, 0,
          0, 0, 0, 1 } 
>

2. 이동 행령

>
    m = { 1, 0, 0, tx,
          0, 1, 0, ty,
          0, 0, 1, tz,
          0, 0, 0, 1 } 
>

3. 회전 행렬

>
    x축 회전
    m = { 1, 0, 0, 0,
          0, cos(x), sin(x), 0,
          0, -sin(x), cos(x), 0,
          0, 0, 0, 1 }

    y축 회전
    m = { cos(y), 0, -sin(y), 0,
          0, 1, 0, 0,
          sin(y), 0, cos(y), 0,
          0, 0, 0, 1 }

    z축 회전
    m = { cos(z), -sin(z), 0, 0,
          sin(z), cos(z), 0, 0,
          0, 0, 1, 0,
          0, 0, 0, 1 }
>

4. 스케일 행령

>
    m = { sx, 0, 0, 0,
          0, sy, 0, 0,
          0, 0, sz, 0,
          0, 0, 0, 1 }
>

## 보간

: 중간 값을 찾는 작업

![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRunosYuxNN5xGfsDtpL_gkwxsL91kk-Xj9li_vCQ68BW4kXuUT)

>
    P = (1-5)*A + t*B
>

## 베지어 곡선

![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQy_g320MRA3JrbrIpJXx2uAD66AYxDOM8pcc0IbRxhGz5UiMcQ)

베지어 곡선은 3개 이상의 제어점으로 곡선의 모양을 정의할 수 있다.

>
    D = A + t*(B - A)
    E = B + t*(C - B)
    P = D + t*(E - D)
>
