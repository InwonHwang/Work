## 의존 (Dependency)
: 클래스 A 가 클래스 B를 포함 하고 있을 떄 클래스 A는 클래스 B에 의존한다고 한다. 보통 상위 모듈은 하위 모듈에 의존한다.

<pre>
<code>

class B
{
public:
  void Temp();
  ...
}

class A
{
  private:
    B* b;
    
  public:
    A() // 클래스 A는 클래스 B를 의존한다. 클래스간 결합도가 높음.
    {
      b = new B();
    }
    
    void Temp()
    {
      b->Temp();
    }
}

</code>
</pre>

아래와 같이 코드를 구현 하는 것을 제어권 역전(IoC:Inversion of Control)이라고 한다.

- 제어권 역전의 장점 (https://ko.wikipedia.org/wiki/%EC%A0%9C%EC%96%B4_%EB%B0%98%EC%A0%84)
1. 작업을 구현하는 방식과 작업 수행 자체를 분리한다.
2. 모듈을 제작할 때, 모듈과 외부 프로그램의 결합에 대해 고민할 필요 없이 모듈의 목적에 집중할 수 있다.
3. 다른 시스템이 어떻게 동작할지에 대해 고민할 필요 없이, 미리 정해진 협약대로만 동작하게 하면 된다.
4. 모듈을 바꾸어도 다른 시스템에 부작용을 일으키지 않는다.

<pre>
<code>

abstract class IB {
  public:
    virtual void Temp() abstract;
}

class B : IB
{
public:
  virtual void Temp();
  ...
}

class A
{
    ...
    void Temp(IB* b)
    {
      b->Temp();
    }   
}

</code>
</pre>

## IoC(Inversion of Control)
: 제어권 역전 방법에는, 여러가지 정형화된 패턴이 있다.

### DI (Dependency Injection)

1. 생성자

<pre>
<code>

class IB { ... }
class B : IB {...}

class A
{
  private:
    IB b;
  
  public:
    A(IB b) // 클래스 A 생성자에서 b를 초기화 한다.
    {
      this.b = b;
    }
}

</code>
</pre>

2. Setter

<pre>
<code>

class IB { ... }
class B : IB {...}

class A
{
  private:
    IB b;
  
  public:
    void Set(IB b) // Set 함수에서 b를 초기화 한다.
    {
      this.b = b;
    }
}

</code>
</pre>

### DL (Dependency Lookup)

저장소에  저장되어있는 빈(Bean)에 접근하기 위해 개발자들이 컨테이너에서 제공하는 API를 이용하여 빈을 Lookup 하는 것
- IoC Container: 객체를 생성, 관리, 의존성을 관리하는 컨테이너.
- 컨테이너 API 를 이용.

<pre>
<code>

// Unity

GameObject gameObj = Instantiate(Resources.Load("temp")) as GameObject;

Transform transform = gameObj.GetComponent<Transform>();

</code>
</pre>


참고
http://homesi.tistory.com/entry/IoCInversion-of-Control-제어의-역전
https://ko.wikipedia.org/wiki/%EC%A0%9C%EC%96%B4_%EB%B0%98%EC%A0%84
