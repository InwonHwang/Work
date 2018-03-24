## 메모리 관리

: C#의 new 로 할당되는 메모리의 경우 GC(Garbage Collector)에 의해 자동으로 해제된다. 주의해야 할 점은 GC가 동작하는 동안 프로그램은 다른 동작을 중지한다는 것이다.

## GC의 동작 방식

: GC는 메모리를 세대를 나누어 관리합니다. 세대는 0세대, 1세대, 2세대 까지있고 처음 힙에 할당되면 0세대 메모리공간에 됩니다. GC가 동작한 후 참조카운트가 0인 객체는 메모리 해제가 이루어지고 참조카운트가 0보다 큰 경우 1세대로 승격하게 됩니다. 2세대로 승격하는 방식은 동일합니다.

<pre>
<code>
class Test {...}

static void Main(string[] args)
{
  Test t = new Test();
    
  Console.WriteLine(GC.GetGeneration(t)); // "0" 세대
  
  GC.Collect();  
  Console.WriteLine(GC.GetGeneration(t)); // "1" 세대
  
  GC.Collect();
  Console.WriteLine(GC.GetGeneration(t)); // "2" 세대
  
  GC.Collect();
  Console.WriteLine(GC.GetGeneration(t)); // "2" 세대
}
</code>
</pre>

: 2세대 이상 승격되지 않으며 2세대 메모리 공간은 시스템이 허용하는 한 계속 커지게 된다. 객체의 크기가 85000바이트의 경우 LOH(Large Object Heap)에 메모리를 할당한다. 메모리가 큰 객체를 별도의 힙에 관리하는 이유는 GC가 동작할때 메모리를 옮기는 압축 과정이 수행이 되는데 메모리 사이즈가 큰 객체의 경우 성능에 큰 손실이 되기 때문이다. 따라서 LOH는 메모리를 하고나서 압축하는 과정이 일어나지 않고 할당된 객체는 바로 2세대에 할당된다. 압축과정이 일어나지 않으므로 메모리 파편화 현상이 일어날 수 있다.

## 비관리 리소스 메모리 관리
: 윈도우 핸들, 파일, 이미지, 폰트 등 비관리 리소스는 GC에 의해 메모리 해제가 되지 않는다.

<pre>
<code>
  // 방법 1
  FileStream fs = new FileStream("test.txt", FileMode.Write);
  
  fs.close(); // 또는 fs.Dispose() 호출하여 메모리를 해제한다.
  
  // 방법 2
  try
  {
    FileStream fs = new FileStream("test.txt", FileMode.Write);
  }
  finally
  {
    fs.Dispose();
  }
  
  // 방법 3: 컴파일 시 방법2로 바뀜.
  using (FileStream fs = new FileStream("test.txt", FileMode.Write))
  {
  }
</code>
</pre>

## SafeHandle / Finalizer
