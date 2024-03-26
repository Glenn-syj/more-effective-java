# 박싱과 래퍼 클래스의 개념 및 활용



## item06 - 불필요한 객체 생성을 피하라

### 🔍 내용 요약

```
똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다. 이전 item에서 생성자 대신 정적 팩터리 메서드를 사용함으로서 객체 생성을 피하는 방법을 학습했었다. 프로그래머는 속도적 측면에서 이점을 얻기 위해 객체 생성을 피하는 방향을 고려해야하지만 이것이 반드시 객체 생성을 피해야 한다는 의미는 아니다. 프로그램의 명확성, 간결성, 기능을 위해 객체를 추가로 생성하는 것이라면 일반적으로 좋은 일이며, 요즘의 JVM에서는 별다른 일을 하지 않는 작은 객체를 생성하고 회수하는 일이 크게 부담되지 않는다. 또한 요즘 JVM의 garbage collector는 최적화가 상당히 잘 되어서 가벼운 객체용을 다룰 때는 직접 만든 객체 풀보다 훨씬 빠르기 때문에 아주 무거운 객체가 아니라면 굳이 객체 풀(pool)을 만들지 않는 것이 좋다.
```

<br>

--------------------------------------------------

### 🧐 심화 탐구

불필요한 객체를 만들어내는 예시로 오토 박싱을 들며 설명을 했었는데, 정렬 알고리즘을 학습하며 가볍게 봤던 래퍼 클래스에 대한 내용이어서 흥미로웠다. 이에 래퍼 클래스 및 박싱과 언박싱, 오토 박싱, 오토 언박싱 등에 대한 이해를 증진하고 어떻게 활용할 수 있을지 알아보는 것을 심화 탐구의 주제로 선정하였다. 

### 1. 래퍼 클래스(Wrapper class)

프로그램에 따라 기본 타입의 데이터를 객체로 취급해야 하는 경우가 있다. 예를 들어 메서드의 파라미터로 객체 타입만 요구되면, 기본 타입의 데이터를 그대로 사용할 수는 없다. 이때는 기본 타입의 데이터를 먼저 객체로 변환한 후 작업을 수행해야 한다. 
자바에서 기본 타입은 byte, short, int, long, float, double, char, boolean 이렇게 8가지가 있으며, 이에 대응되는 래퍼 클래스 Byte, Short, Integer, Long, Float, Double, Character, Boolean이 있다. 
래퍼 클래스의 구조도는 Object 클래스를 Boolean, Character, Number 클래스 등이 상속 받고 있으며 다시 Number 클래스를 Byte, Short, Integer, Long, Float, Double, BigInteger, BigDecimal 클래스 등이 상속 받고 있다. 

#### 흥미로웠던 점
오라클 자료를 찾아보며 Boolean, Character 클래스의 경우 Serializable, Comparable<> 인터페이스를 구현한 것으로 나왔다. 
Number 클래스의 경우 Serializable만 구현된 반면 Integer 등의 래퍼 클래스에서는 모두 Comparable<>을 구현하고 있었다. 
또, Number 클래스의 메서드는 기본자료형Value() 의 꼴인데 byteValue()와 shortValue()는 Modifier and Types(제어자와 자료형)가 각각 byte와 short인 반면, 다른 자료형에 대해서는 abstract 키워드 달려있었다. 
메서드 설명에서 두 메서드만 This implementation returns the result of intValue() cast to a byte 라는 문장이 달려 있는데, 내 생각에는 Number 클래스의 abstract intValue()를 Integer 클래스의 intvalue()에서 구현하고 이걸 타입 캐스팅한걸 byteValue()와 shortValue()에서 반환해서 둘 메서드만 abstract가 필요가 없었던 것 같다.(근데 메서드간 관계도가 상당히 복잡해서 아닐 수도 있음) 

### 2. 박싱(Boxing), 언박싱(UnBoxing)

래퍼 클래스는 산술 연산을 위해 정의된 클래스가 아니므로, 인스턴스에 저장된 값을 변경할 수 없다. 단지, 값을 참조하기 위해 새로운 인스턴스를 생성하고, 생성된 인스턴스의 값만을 참조할 수 있다. 기본 타입의 데이터를 래퍼 클래스의 인스턴스로 변환하는 과정을 박싱(Boxing)이라고 하며, 래퍼 클래스의 인스턴스에 저장된 값을 다시 기본 타입의 데이터로 꺼내는 과정을 언박싱(UnBoxing)이라고 한다. 

### 3. 오토 박싱(AutoBoxing), 오토 언박싱(AutoUnBoxing)

JDK 1.5부터는 박싱과 언박싱이 필요한 상황에서 자바 컴파일러가 이를 자동으로 처리해주는데 이렇게 자동화된 박싱과 언박싱을 오토 박싱(AutoBoxing)과 오토 언박싱(AutoUnBoxing)이라고 부른다. 
내 생각에는 컴파일러가 처리해주는 원리까지는 이해할 필요없고, (오라클에서 찾을 수 없음?) 오토 박싱과 오토 언박싱이 프로그램 성능을 떨어뜨릴 수 있으므로, 이를 주의할 필요만 있을 것 같다.(p.34) 

### 4. 활용

```java
package item06;

import java.util.Arrays;
import java.util.Collections;

public class Test {
    public static void main(String[] args) {

        // 박싱(사용 자제 API로 지정됐지만 실행 가능)
        // 생성자를 이용한 경우로 두 인스턴스는 다른 객체
        Integer num1 = new Integer(1);
        Integer num2 = new Integer(1);
        System.out.println(num1);            // 1
        System.out.println(num2);            // 1
        System.out.println(num1 == num2);    // false

        // 박싱(오토 박싱이 가능해서 노란줄 뜸)
        // 정적 팩터리 메서드를 이용한 경우로 두 인스턴스는 같은 객체
        Integer num3 = Integer.valueOf(2);
        Integer num4 = Integer.valueOf(2);
        System.out.println(num3);            // 2
        System.out.println(num4);            // 2
        System.out.println(num3 == num4);    // true

        // 언박싱(오토 언박싱이 가능해서 노란줄 뜸)
        int num5 = num1.intValue();
        int num6 = num3.intValue();
        System.out.println(num5);    // 1
        System.out.println(num6);    // 2

        // 오토 박싱(두 인스턴스는 같은 객체)
        Integer num7 = 3;
        Integer num8 = 3;
        System.out.println(num7);            // 3
        System.out.println(num8);            // 3
        System.out.println(num7 == num8);    // true

        // 오토 언박싱
        int num9 = num8;
        System.out.println(num9);    // 3

        // 오토 박싱과 오토 언박싱을 통한 기본 타입과 래퍼 클래스 간의 연산
        Integer num11 = new Integer(2);
        Integer num12 = new Integer(7);
        int num13 = 3;
        System.out.println(num11 + num12);    // 9
        System.out.println(num11 * num13);    // 6
        System.out.println(num12 / num13);    // 2



        // 래퍼 클래스의 활용

        // 문자열을 기본 타입 값으로 변환
        String str1 = "123";
        int n = Integer.parseInt(str1);

        // 배열을 내림차순으로 정렬
        Integer[] arr = {2, 5, 3, 1, 4};                 // int는 기본 타입(primitive type)
        Arrays.sort(arr, Collections.reverseOrder());    // Collections는 Object를 상속한 클래스(ex 래퍼 클래스)에 대해 사용 가능한 인터페이스
        System.out.println(Arrays.toString(arr));        // [5, 4, 3, 2, 1]

    }
}

```

참고자료 : https://docs.oracle.com/javase/8/docs/api/

참고자료 : https://tcpschool.com/java/java_api_wrapper#google_vignette

참고자료 : https://coding-factory.tistory.com/547

--------------------------------------------------

### 🧠 어려웠던 점

- 오토 박싱과 오토 언박싱이 등장하며 프로그래머가 래퍼클래스에 대해 충분히 이해할 필요가 있을지 의문이 들어 주제 선정 및 방향성에 어려움이 있었다.
- 교재 p.34의 코드를 실행해보며 유의미한 시간차이가 존재함을 확인할 수 있었다. 하지만 그 이유에 대해서는 이해가 잘 되지 않는데 System.identityHashCode() 메서드를 통해 주소값을 찾으려고 했을 때, long과 Long모두 동일하게 출력이 되어 메모리적으로 찾기는 어려웠다. 이에 자료들을 찾아보다가 아래 참고 자료의 글이 그나마 납득이 되어서 가져왔다. 불필요한 Long 인스턴스가 생성된다는 것이 사실일 경우 sum += i의 연산이 끝났을 때 생성이 되어야 하는데 이때 내부적으로 sum이 먼저 오토 언박싱이 되고 i와 덧셈 연산 수행 후 다시 Long으로 오토 박싱이 된다면 납득 가능한 설명인 것 같았다. 

참고자료 : https://velog.io/@skyepodium/%EC%9E%90%EB%B0%94-%EB%B0%95%EC%8B%B1-%EC%96%B8%EB%B0%95%EC%8B%B1%EC%9D%80-%EB%82%AF%EC%84%A4%EC%96%B4%EC%84%9C

