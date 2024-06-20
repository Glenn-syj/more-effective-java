## 확장 가능한 열거 타입에 대한 이해

### 정리

**내용 요약**

_열거 타입_ 은 '거의 모든' 상황에서 _타입 안전 열거 패턴_ 보다 우수하다.

그러나 단 하나의 예외가 있으니, _타입 안전 열거 패턴_ 은 '확장'이 가능하지만, _열거 타입_ 은 확장이 불가능하다.

이러한 문제점을 해결하기 위해서는 인터페이스를 이용해 확장 가능한 _열거 타입_ 을 흉내내야 한다.

_열거 타입_ 을 직접 확장하는 것은 할 수 없지만, _열거 타입_ 을 통해 인터페이스를 구현한다면, _열거 타입_ 대신 인터페이스를 확장할 수 있기 때문에 인터페이스를 이용하면 된다.

인터페이스를 이용하여 확장 가능해진 열거 타입 코드의 예시는 다음과 같다.

```java
public enum ExtendedOperation implements Operation{
    EXP("^"){
        public double apply(doublex, doubley)
            return Math.pow(x, y)
        }

    REMAINDER("%") {
        public double apply(double x, doubley){
            return x % y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol){
        this.symbol = symbol;
    }

    @Override public String toString(){
        return symbol;
    }

}
```

개별 인스턴스 수준에서만이 아닌, 타입 수준에서도 기본 열거 타입 대신 확장된 열거 타입을 넘겨 확장된 열거 타입의 원소를 모두 사용하게 할 수 있다.

다음은 확장된 열거 타입을 매개변수로 넘겨주어 확장된 열거 타입의 모든 원소를 테스트하는 기능을 구현하고 있다.

```java
public static void main(String[] args){
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]);
    test(ExtendedOperation.class, x, y);
}

private static <T extends Enum<T> & Operation> void test(Class<T> opEnumType, double x, doubley) {

    for(Operation op : opEnumType.getEnumConstants())
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));

```

`<T extends Enum<T> & Operation> Class<T>` 는 Class 객체가 열거 타입인 동시에 Operation의 하위 타입이어야 함을 의미한다.

또 다른 방법으로는 Class 객체 대신 한정적 와일드카드 타입을 넘기는 것이 있다.


```java
public static void main(String[] args){
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]);
    test(Arrays.asList(ExtendedOperation.values()), x, y);
}

private static void test(Collection<? extends Operations> opSet, double x, double y) {
    for(Operation op : opSet)
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
``` 


Class 객체를 넘기는 대신 한정적 와일드카드 타입을 넘기는 것은 여러 구현 타입의 연산을 조합해 호출할 수 있도록 한다. 그러나, 대신 특정 연산에서는 EnumSet과 EnumMap을 사용하지 못한다.


'확장 가능한 열거 타입'을 사용하는 것에도 문제는 존재한다.

열거 타입끼리는 구현을 상속할 수 없다는 점이다.

열거 타입끼리 구현을 상속할 수 없다는 문제의 대안으로 '아무 상태에도 의존하지 않는 경우'에는 '디폴트 구현'을 이용하여 인터페이스에 추가하는 방법이 있다.


### 심화 탐구


**출발점**

item 38은 분량이 많지 않았지만, 어려운 개념과 원리가 많았다.

특히 읽으면서 이해가 되지 않는 것 세 가지가 존재하였다.

1. `확장한 타입의 원소는 기반 타입의 원소로 취급하지만 그 반대는 성립하지 않는다면 이상하지 않은가!`

해당 문구에 대한 이해가 어려웠다. 일반적인 자바의 상속 원리를 설명한 위 문구는 '열거 타입'은 위와 같은 상속 원리를 적용하지 않기 때문에 확장이 불가능하다는 것을 뜻하고 있다.

그러나, 이 상속 원리가 없기 **때문에** '열거 타입'을 확장할 수 없다는 것을 이해하지 못하였다.


2. `Class 객체를 넘기는 코드와 비교하면 여러 구현 타입의 연산을 조합해 호출할 수 있게 되었다. 하지만, 특정 연산에서는 EnumSet과 EnumMap을 사용하지 못한다.`

Class 객체를 매개변수로 넘기는 것과, 한정적 와일드카드 타입을 매개변수로 넘기는 것에 대한 차이를 설명하는 이 문구를 이해하지 못했다.

Class 객체를 매개변수로 넘긴다는 것과, 한정적 와일드카드 타입을 매개변수로 넘긴다는 것에 대한 의미를 파악하지 못하였고, 

이것이 어떻게 여러 구현 타입의 연산을 조합해 호출할 수 있게 하는지,

그러나 특정 연산에서 EnumSet과 EnumMap을 사용하지 못하는 이유는 무엇인지 이해하지 못하였다.


3. `서로 상속할 수 없다는 문제의 대안으로 '아무 상태에도 의존하지 않는 경우' 에는 '디폴트 구현(item 20)'을 이용하여 인터페이스에 추가하는 방법도 존재한다.`

이전에 다룬 item 20에 존재하는 개념인 `디폴트 구현`에 대해 정의가 흐릿해졌기에 디폴트 구현을 이용하여 서로 상속할 수 없는 문제를 어떻게 해결하는지 파악하지 못하였다.

그러므로 '확장 가능한 열거 타입에 대한 이해'를 위하여 위 세 가지 부분에 대해 탐구해보도록 하겠다.


**설명**

1. `확장한 타입의 원소는 기반 타입의 원소로 취급하지만 그 반대는 성립하지 않는다면 이상하지 않은가!`

먼저 '확장한 타입의 원소는 기반 타입의 원소로 취급하지만 그 반대는 성립하지 않는' 원리에 대해 알아보겠다.

열거 타입과 타입 안전 열거 패턴에 대해 학습하는 파트인 만큼 '타입 안전 열거 패턴'에 원리를 적용해보겠다.

```java
class EffectiveStudy{

    public static final EffectiveStudy glenn = new EffectiveStudy("glenn");
    public static final EffectiveStudy yng = new EffectiveStudy("yng");

    private String name;

    protected EffectiveStudy(String name){
        this.name = name;
    }
}
```

만일 이러한 타입 안전 열거 패턴이 존재한다고 하면, 다른 클래스를 이용하여 해당 타입 안전 열거 패턴을 확장시킬 수 있다.

```java
class MoreEffectiveStudy extends EffectiveStudy{

    public static final MoreEffectiveStudy undead = new MoreEffectiveStudy("undead");
    public static final MoreEffectiveStudy bobo = new MoreEffectiveStudy("bobo");

    private MoreEffectiveStudy(String name){
        super(name);
    }

}
```

타입 안전 열거 패턴의 확장성을 확인했으니 위에서 언급한 저자의 말로 돌아가보자.

`확장한 타입의 원소는 기반 타입의 원소로 취급`

이 부분부터 쪼개어 살펴본다면, 위에 코드에서는 이렇게 표현할 수 있을 것이다.

MoreEffectiveStudy 클래스에서 생성한 undead와 bobo는 EffectiveStudy 타입의 원소로 취급하지만

`그 반대는 성립하지 않는`

EffectiveStudy 클래스에서 생성한 glenn과 yng는 MoreEffectiveStudy의 원소로 취급할 수 없다는 것이다.

이는 객체 지향 프로그래밍의 기본 원리인 '상속'과 '다형성'에 해당하는 현상이다.

---

비록, 해당 문구에 대하여 조사했음에도 정보를 얻을 수 없었지만, 

맥락을 파악해본다면 저자는 고정된 상수의 집합을 유지해야 하는 '열거 타입'이 확장을 하려고 하면서 일관성이 상실되는 것 자체를 '이상한 것'이라 표현한 것 같다.

---



### 어려운 점

`확장한 타입의 원소는 기반 타입의 원소로 취급하지만 그 반대는 성립하지 않는다면 이상하지 않은가!`

해당 문구에 대한 정확한 뜻을 파악하고 싶었지만, 어디에서도 그 정보를 찾을 수 없어서 쉽지 않았다.

맥락을 통해서 문구에 대한 나의 생각을 정리해보았지만, 검증하기 위한 자료가 없다보니 확신할 수는 없다.

추가로, 시간관계상 한 가지에 대해서만 탐구하고 말았다.

이후 차근차근 다른 탐구사항들에 대해서도 조사 후 정리하여 갱신하도록 하겠다.

---

References : 




