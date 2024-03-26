## 정적 팩터리 메서드 방식을 사용한 싱글턴의 장점에 대해

### 정리

**내용 요약**

자주 사용되는 싱글턴 패턴 방식은 구성 방식이 세 가지가 존재한다.
1. 첫 째는 public static final 멤버 변수에 유일한 객체를 할당하는 방식이다.

```java
public class Elvis{
    public static final Elvis INSTACE = new Elvis();
    private Elvis(){...}

}
```
+ public static final 방식은 해당 클래스가 싱글텀임이 API에 명백히 드러난다는 것과 간결하다는 장점이 있다.
+ 그러나 이 방식은 권한이 있는 클라이언트가 AccessibleObject.setAccessbiel을 이용해 private 생성자를 호출할 수 있다는 위험성이 있다.

2. 둘 째는 생성자를 private으로 접근제한하여, 다른 객체를 생성하지 못하도록 하고, 생성한 객체를 정적 팩터리 메서드로 반환하느 것이다.

```java
public static Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis(){...}
    public static Elvis getInstace(){return INSTANCE;}

}
```
+ 두 번째 방식인 정적 팩터리 방식은 여러 장점을 가지고 있다.
    + API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
    + 정적 팩터리를 제너릭 싱글턴 팩터리로 만들 수 있다.
    + 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

3. 세 번째 방식은 원소가 하나인 열거타입을 선언하는 것이다.

```java
public enum Elivs{
    INSTANCE;
}
```

+ 원소가 하나인 열거타입을 선언하는 이 방식은 public static final 방식과 비슷하지만 더 간결하고 추가 노력없이 직렬화 할 수 있으며, 제 2의 인스턴스를 완전히 막아준다.


### 심화 탐구

[장점 2에 대한 이해와 예제 코드를 생성하기 위해 chatGPT를 참고하였습니다.]

**출발점**

싱글턴 패턴을 만드는 방식 중, 두 번째 방식인 정적 팩터리 메서드를 사용하는 것은, 각종 프로젝트와 과제에서 많이 사용했던 방식이다.

그러나 이러한 정적 팩터리 메서드를 사용한 싱글턴 패턴에 대한 장점들을 알고 있지 않았다.

1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
2. 정적 팩터리를 제너릭 싱글턴 팩터리로 만들 수 있다.
3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

그러나 본문에서 제시되는 장점들은 이것이 어떤 식으로 이루어지고 있는지 알 수 없거나, 이것이 왜 장점인지 이해가 가지 않고, 또는 이 문장 자체가 무슨 의미인지 파악할 수 없었다.

따라서, 앞으로도 자주 사용하게 될 정적 팩토리 메서드를 사용한 싱글턴 패턴의 장점의 의미를 이해해보겠다.


**설명**


1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
```java
package test;

public class StaticPactoryMethod {
	private static final StaticPactoryMethod spm = new StaticPactoryMethod();
	
	private StaticPactoryMethod() {
		System.out.println("생성");
	}
	
	public static StaticPactoryMethod getInstance() {
		return spm;
	}
	
	public static void main(String[] args) {
		StaticPactoryMethod newSpm =  getInstance();
	}
}

# output: 생성
```

+ 이것이 보통 private static final을 이용한 싱글턴 패턴이다.
+ 본문에 나온 'API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다'는 말은,
    + getInstance 메서드의 반환값만을 변경하여 손쉽게 싱글턴이 아닌게 만들 수 있다는 뜻이다.

```java
package test;

public class StaticPactoryMethod {
	private static final StaticPactoryMethod spm = new StaticPactoryMethod();
	
	private StaticPactoryMethod() {
		System.out.println("생성");
	}
	
	public static StaticPactoryMethod getInstance() {
		return new StaticPactoryMethod();
	}
	
	public static void main(String[] args) {
		StaticPactoryMethod newSpm =  getInstance();
	}
}

# output: 생성\n생성
```
+ 이처럼 getInstance 메서드에서 이미 만들어진 spm을 반환하는 것이 아닌 생성자를 이용해 새로운 객체를 생성해 반환하도록 하면 싱글턴 패턴에서 벗어날 수 있다. 


2. 정적 팩터리를 제너릭 싱글턴 팩터리로 만들 수 있다.

+ 우선 위에서 작성한 예시 코드를 제너릭 싱글턴 팩터리로 만들어보겠다.

```java
package test;

public class StaticPactoryMethod<T> {
	
	private static final StaticPactoryMethod<?> spm = new StaticPactoryMethod<>();
	
	private StaticPactoryMethod() {
		System.out.println("생성");
	}
	
	public static <T>StaticPactoryMethod<T> getInstance() {
		return (StaticPactoryMethod<T>)spm;
	}
	
	public static void main(String[] args) {
		StaticPactoryMethod<String> newSpm =  StaticPactoryMethod.getInstance();
		StaticPactoryMethod<Integer> newSpm2 =  StaticPactoryMethod.getInstance();
	}
}
```

+ 이렇게 제너릭 팩토리 메서드를 사용하면, 간편하게 여러 타입의 클래스에 대한 객체를 싱글턴 패턴으로 관리할 수 있게 된다.

+ 또한 이를 통해, 원하는 타입의 객체를 싱글턴 패턴으로 가져올 때마다 직접 형변환을 해주지 않아 코드의 중복을 최소화할 수 있다.


3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

+ 공급자란 인자를 받지 않고 제네릭 타입의 객체를 반환하는 함수형 인터페이스이다.
    + 함수형 인터페이스는 한 개의 추상 메서드를 가지고 있는 인터페이스를 말한다.

+ 공급자를 사용하면 공급자를 통해 반환할 값이 필요할 때까지 연산을 지연시킬 수 있다는 장점이 있다.

+ 공급자를 사용하여 연산을 지연시켜야 하는 대표적인 예로는, 시간측정이 있다.
    
    + 나이를 계산한다고 할 때, 객체가 생성된 이후에 시간이 흘러서 나이가 증가할 수도 있다. 
    + 이 때, 현재 시점을 기준으로 나이를 측정하는 메서드를 바로 실행하지 않다가, 
    + 현재 나이를 출력해야 할 때 실행하도록 하는 것이다.


### 출처
https://stackoverflow.com/questions/40244571/when-we-should-use-supplier-in-java-8