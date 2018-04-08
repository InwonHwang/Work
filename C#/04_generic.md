## Gerneric

C# 1.0에서 박싱, 언박싱 문제에 대해서 포스팅을 작성한 적이 있습니다.
ArrayList 컬렉션 또한 이러한 문제점을 갖고 있습니다. 값 타입을 저장할 때 박싱과정이 일어납니다.

<pre>
<code>

public virtual int Add(object value);

</code>
</pre>

C# 2.0 이러한 문제를 쉽게 해결하기 위해 제네릭이라는 개념을 도입했습니다.
C# 1.0 에서는 박싱 / 언박싱 문제를 해결하기 위해서 값타입에 해당하는 컬렉션을 모두 구현해야 했습니다.

<pre>
<code>

public class IntList
{
    int[] _list;
    ...
}

public class FloatList
{
    float[] _list;
    ...
}

</code>
</pre>

C# 2.0에서는 제네릭을 활용하여 값타입에 해당하는 컬렉션을 모두 구현해야 하는 수고를 덜하게 되었습니다.

<pre>
<code>

public class MyList<T>
{
    T[] _list;
    ...
}

</code>
</pre>

T에 대응되는 타입에 대한 코드가 컴파일시에 자동으로 만들어진다.

## 제약조건

C# 제네릭은 제네릭 타입 T에 대해서 다음과 같이 형식을 제한 할 수 있습니다.
이와 같이 형식을 제한함으로서 문제를 컴파일 단계에서 확인 할 수 있다.

<pre>
<code>

public class Heap<T> where T : ICompareable, IComparable<T>
{
    T[] _list;

    ...

    private void heapify(int idx)
    {
        ...

        if (_list[idx].CompareTo(_list[parentIdx]) >= 0) ...

        ...
    }
}

</code>
</pre>

## 궁금한점

제네릭 컬렉션은 참조타입에 대해서도 코드를 확장을 하는가?
