### 의존 (Dependency)
: 클래스 A 가 클래스 B를 포함 하고 있을 떄 클래스 A는 클래스 B에 의존한다고 한다.

<pre>
<code>

// 의존 관계

class B
{
  ...
}

class A
{
  B b;
  ...
}

</code>
</pre>

### 의존성 주입 (Dependency Injection)
: 클래스 A가 클래스 B에 의존 한다고 할 때 클래스 A 내부에 있는 클래스 B를 초기화하는 것을 의존성 주입이라고 한다.

### IoC(Inversion of Control)
: 의존성을 주입을 제어하는 방법, 여러가지 정형화된 패턴이 있다.

1. 생성자에 의한 주입

<pre>
<code>

class B {...}

class A
{
  private:
    B b;
  
  public:
    A(B b) // 클래스 A 생성자에서 b를 초기화 한다.
    {
      this.b = b;
    }
}

</code>
</pre>

2. Setter에 의한 주입

<pre>
<code>

class B {...}

class A
{
  private:
    B b;
  
  ...
  
  public:
    void Set(B b) // Set 함수에서 b를 초기화 한다.
    {
      this.b = b;
    }
}

</code>
</pre>

3. Singleton에 의한 주입
: 생성자 내부 또는 함수 내부에서 초기화 할 수 있다.

<pre>
<code>

class B
{
  public:
    static B instance;    
    ...
}

class A
{
  private:
    B b;
  
  ...
  
  public:
    void A()
    {
      this.b = B.instance;
    }
  
    void Set(B b) // Set 함수에서 b를 초기화 한다.
    {
      this.b = B.instance;
    }
}

</code>
</pre>

4. 인터페이스에 의한 주입
: 컨테이너가 내부 데이터로 있을 때 인터페이스를 구현하여 의존성을 주입한다.

<pre>
<code>

Class Base

class DeriveA : public Base { ... }
class DeriveB : public Base { ... }
class DeriveC : public Base { ... }

class A
{
  private:
    list<Base> list;
  
  ...  
  public:
    void Register(Base b)
    {
      list.push_back(b);
    }
}

</code>
</pre>

