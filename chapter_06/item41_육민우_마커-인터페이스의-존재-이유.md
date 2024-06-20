# 마커 인터페이스의 존재 이유



## item41 - 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라

### 🔍 내용 요약

**아무 메서드도 담고 있지 않고, 단지 자신을 구현하는 클래스가 특정 속성을 가짐을 표시해주는 인터페이스를 마커 인터페이스(marker interface)라 한다.**
좋은 예는 Serializable 인터페이스로 Serializable 인터페이스는 자신을 구현한 클래스의 인스턴스는 ObjectOutputStream을 통해 쓸(write) 수 있다고, 즉 직렬화(serialization)할 수 있다고 알려준다. 

다음은 Serializable 인터페이스의 내부 코드이다.(정말 아무것도 없다) 

```java
public interface Serializable {
}
```

Cloneable 인터페이스 역시 내부에 아무것도 없어서 마커 인터페이스로 생각되는데 item13 p.77에서는 mixin interface라는 표현을 사용했다. 

```java
public interface Cloneable {
}
```

마커 애너테이션(item 39)이 등장하면서 마커 인터페이스는 구식이 되었다는 이야기가 있는데 이는 사실이 아니며 마커 인터페이스는 마커 애너테이션보다 나은 두 가지 장점이 있다. 

<br>

1. 마커 인터페이스는 이를 구혀한 클래스의 인스턴스들을 구분하는 타입으로 쓸 수 있으나, 마커 애너테이션은 그렇지 않다.
2. 적용 대상을 더 정밀하게 지정할 수 있다.

<br>

반면 마커 애너테이션은 거대한 애너테이션 시스템의 지원을 받는다는 점에서 마커 인터페이스보다 나은 점도 있다. 

그렇다면 마커 애너테이션과 마커 인터페이스 중 어떤 것을 사용해야할지 고민을 하게 되는데, **새로 추가하는 메서드 없이 단지 타입 정의가 목적이라면 마커 인터페이스를 선택**하면 된다. 
클래스나 인터페이스 외의 프로그램 요소에 마킹해야 하거나, 애너테이션을 적극 활용하는 프레임워크의 일부로 그 마커를 편입시키고자 한다면 마커 애너테이션이 올바른 선택이다. 
적용 대상이 ElementType.TYPE인 마커 애너테이션을 작성하고 있다면, 잠시 여유를 갖고 마커 애너테이션과 마커 인터페이스 중 어떤 것이 나을지 고민해보자. 

<br>

--------------------------------------------------

### 🧐 심화 탐구

교재의 개념을 학습하면서 아무것도 없는 마커 인터페이스가 무슨 의미가 있나라는 생각이 들었다. 추가로 마커 인터페이스는 내부에 아무 내용도 없는데 그냥 구현했다고 implement를 해도 컴파일 에러가 발생하지 않는데 오히려 문제가 발생할 여지가 있는 것은 아닌가 싶었다. 그래서 마커 인터페이스가 어떤 활용도가 있을지 알아보는 것을 심화 탐구의 주제로 선정했다. 



필자가 첫 번째 장점으로 소개한 **마커 인터페이스는 이를 구현한 클래스의 인스턴스들을 구분하는 타입으로 쓸 수 있으나, 마커 애너테이션은 그렇지 않다.** 이 부분은 얼핏 장점이 될 수 있을 것으로 보였다. 마커 애너테이션은 불가능하다는 점을(앞으로도 불가능한지 여부) 확실히 이해하지는 못했지만, 마커 인터페이스는 그냥 인터페이스이므로 이를 구현한 클래스들은 마커 인터페이스 타입으로 구분이 가능하기 때문에 기능의 확장 혹은 제한의 개념으로 사용할 수 있을 것 같다. 이에 대해 구체적인 아이디어가 잘 떠오르지 않아서 ChatGPT에서 ObjectOutputStream 의 사용법에 대한 코드를 받아서 가공해서 사용했다. 

```java
package item41;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.Serializable;
import java.util.Arrays;

public class Test {

    // Serializable을 구현하지 않은 클래스
    static class Node1 {
        int v = 1;
    }

    // Serializable을 구현한 클래스
    static class Node2 implements Serializable {
        int v = 2;
    }

    public static void main(String[] args) throws IOException {

        Node1 node1 = new Node1();
        Node2 node2 = new Node2();

        ByteArrayOutputStream baos1 = new ByteArrayOutputStream();
        ByteArrayOutputStream baos2 = new ByteArrayOutputStream();

        ObjectOutputStream oos1 = new ObjectOutputStream(baos1);
        ObjectOutputStream oos2 = new ObjectOutputStream(baos2);

//        oos1.writeObject(node1);  // NotSerializableException 발생
        oos2.writeObject(node2);  // 직렬화 성공
        
        // 출력 성공
        System.out.println(Arrays.toString(baos2.toByteArray()));
    }
}
```

주석의 내용처럼 `implements Serializable` 이 코드를 넣은 것만으로 Node 클래스가 직렬화가 되어서 이러면 무슨 의미가 있나 싶었는데, 오히려 `implements Serializable` 를 넣지 않으면 직렬화가 되지 않아서 직렬화를 하고 싶지 않은(혹은 하면 안되는) 객체에 대해 직렬화를 기본적으로 막는다는 답변을 받았다. 

이에 대해 제법 합리적이라고 느꼈는데, 알고리즘 학습 과정에서도 사용자가 정의한 클래스에 대해 인스턴스간의 비교를 위해 `Comparable` 을 사용하는데 `Comparable` 없이도 객체간의 비교가 기본적으로 제공되면 오히려 런타임에 버그를 찾기 어려울 것으로 생각이 되었다. 

--------------------------------------------------

출처

https://docs.oracle.com/javase/8/docs/api/

ChatGPT

--------------------------------------------------

### 🧠 어려웠던 점

마커 인터페이스의 경우 자바가 기본적으로 제공하는 마커 인터페이스를 개발자가 `implements` 할지 여부를 결정하는 것이 중요해보였는데, 개발자가 마커 인터페이스를 굳이 정의해서 활용할 필요가 있을지 고민이 되었는데 해답을 얻기 어려웠다. 
