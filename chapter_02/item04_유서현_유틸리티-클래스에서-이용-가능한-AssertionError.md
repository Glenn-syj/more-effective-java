## 유틸리티 클래스에서 이용 가능한 AssertionError


`Effective Java` 2장 객체 생성과 파괴 - '아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라' 를 읽고

### 정리

**내용 요약**

- 정적 메서드 · 필드만을 담은 클래스를 유틸리티 클래스라고 한다.
    - 기본 타입 값이나 배열 관련 메서드들을 모아두는 java.lang.Math, java.util.Arrrays 가 대표적인 예시다.
    - java.util.Collections처럼 특정 인터페이스를 구현하는 객체를 생성해주는 정적 메서드를 모아둘 수도 있다.
    - final 클래스를 상속해서 하위 클래스에 메서드를 넣기 불가능하므로, 그와 관련한 메서드들을 모아둘 때도 사용한다.

- 유틸리티 클래스는 객체 생성용으로 설계한 것이 아니므로, 컴파일러가 기본 생성자(public 생성자)를 추가하지 않도록 private 생성자를 추가해 클래스의 인스턴스화를 막을 수 있다.

- 다만 클래스 내에서 실수로라도 생성할 수 있으므로, 그 private 생성자 내에서 Error를 던져 방지 가능하다.
    
    ```java
    public Class UtilityClass {
    
    	private UtilityClass() {
    		throw new AssertionError();
    	}
    
    }
    ```

### 심화 탐구

**출발점**

- 유틸리티 클래스를 이용 시 던지는 에러인 `throw new AssertionError();` 에서 AssertionError란 무엇이고 어떤 방식으로 이용 가능한지 추가로 조사해보았다.

**설명**

- **AssertionError**

    - *assertion*의 사전적 정의는 *‘a statement that you strongly believe is true’*, 즉 '진실이라고 강력하게 믿는 문장’을 의미한다. 한국어로는 ‘표명’, ‘가정 설정문’으로 등재되어 있다.

    - 컴퓨터 프로그래밍에서 특정 지점에 위치한 `assertion`은 해당 지점에서 개발자가 반드시 참(true)이어야 한다고 생각하는 사항을 표현한 논리식이다. 자바 뿐 아니라 대부분의 언어에서 각자의 assertion 체크 기능을 지원한다고 한다. assertion이 위반되는 경우(즉, 논리식 결과가 거짓) **런타임 에러**에 해당하며, 프로그램에 버그나 논리적 문제가 있는 것을 암시한다.

- 사용법

    - `AssertionError()` : 아무 매개변수 없이 사용 시, 세부 메시지 없이 AssertionError를 생성한다.

    - `AssertionError(Object detailMessage)` : 매개변수에 Object(기본 자료형들도 가능)를 넣어 사용 시, 객체에서 정의된 세부 메시지를 사용하여 AssertionError를 구성하고 메시지는 문자열로 변환된다.

    - `AssertionError(String message, Throwable cause)` : 세부 정보 메시지와 원인으로 새 assertion 에러를 구성한다.

<br>

**어려웠던 부분**

- 유틸리티 클래스를 구체적으로 어떤 상황에 쓰는지 잘 떠오르지 않기에, 예시 코드 위주로 탐구해 볼 예정이다.


<br>


**참고 자료**

*[https://ko.wikipedia.org/wiki/표명](https://ko.wikipedia.org/wiki/%ED%91%9C%EB%AA%85)*

*https://docs.oracle.com/javase%2F7%2Fdocs%2Fapi%2F%2F/java/lang/AssertionError.html*