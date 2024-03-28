## 의존 객체 주입이란 무엇인가?

### 정리

**내용 요약**

많은 클래스가 하나 이상의 자원에 의존한다. 가령 맞춤법 검사기는 사전에 의존하는데 사전의 종류가 한개라면 싱글톤과 유틸리티 클래스 만으로 가능하겠지만 다양한 사전이 필요하기 때문에 사용하는 자원에 따라 동작이 달라지는 경우 두가지 방법은 적합하지 않다.

대신 인스턴스를 생성할때 생성자에 필요한 자원을 넘겨주는 의존 객체주입을 사용해야한다. 단 의존성이 수천개나 되는 큰프로젝트에서는 코드가 어지러워 질수 있으니 대거,스프링등 의존 객체 주입 프레임워크를 사용해서 어지러움증을 해소 가능하다. 

### 심화 탐구

**출발점**

스프링, 자바등에서 계속해서 나오는 의존성 주입에 대한 개념이 나오고 있다. 어느정도 정보를 외부에서 주입한다고 추상적으로만 생각했지 그동안 깊이 있게 고민을 해본적이 없는것 같았다. 따라서 의존성 주입에 대한 탐구를 해보기로 했다. 



**설명**

1. 의존성 주입이란? 

	의존성 주입이란 기본적인 ‘외부'에서 클라이언트에게 서비스를 제공(주입)하는 것이다.
	다시 말해, 객체가 필요로 하는 어떤 것을 외부에서 전달해주는 것으로 볼 수 있다. 

	그렇다면 이러한 의존성 주입을 왜 할려고 할까? 바로 모듈간의 결합을 조금더 느슨하게 만들어서 서로 상호작용을 쉽게 만들기 위해서다. 

	만약 싱글톤을 통해서 모듈간의 결합을 강하게 만든다면 하나의 모듈의 변경사항이 다른 모듈에 영향을 미칠수 있다. 따라서 우리는 의존성 주입자를 통해 이들의 결합을 좀 더 느슨하게 할수있다.  즉 메인 모듈이 직접 다른 모듈에게 의존성을 주기보다는 의존성 주입자가 간접적으로 주기 때문에 메인 모듈과 하위 모듈간의 의존성을 떨어지게 한다. 

	기본적으로 의존성 주입은 생성자 주입, setter 주입, 필드주입 3가지  있다고 한다. 그중 앞에서는 싱글톤패턴에 대해 알아보며 생성자에 대해 탐구해 보았으니 이번에는 setter에 대해 알아 보았다.

	
 



2. getter와 setter

	클래스의 맴버변수가 private로 할당될시 우리는 다른 클래스에서 pt.a=10 이런식으로 값을 할당할수 없고 
	같은 클래스에서 할당된 메서드(setter, getter) 들을 통해 접근하여 값을 받고 할당할수 있다. 

	```
	package java_getter_setter;

	class PrivateTest2 {
	private int a;
	
	//setter
	public void setA(int a) {
		this.a = a;
	}

	// getter
	public int getA() {
		return a;
	}

	
	public void printData() {
		System.out.println("a: " + a);
		
	}
	}

	public class PrivateTest1 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		PrivateTest2 pt = new PrivateTest2();
		pt.printData();
        
	//		pt.a = 10;

		pt.setA(10);
		
		pt.printData();

		//private 멤버 클래스 밖에서 사용 불가
        
		System.out.println("pt.a: " + pt.getA());
		

	}
	}
	```


	이렇게 되면 a값을 직접 바꾸는 것이 아닌 setter를 사용하여 '간접'적으로 바꾸기 때문에 의존성 주입의 	개념인 외부인자가 간접적으로 
	값을 주기 때문에 직접 영향을 주는 것이 아닌 간접적인 즉 느슨한 결합을 할수 있게 되는것이다. 
	이경우 setter메서드에 외부에서 할당하는 값에 대한 조건을 설정하여 프로그램의 안정성도 높일수 있다.

3. setter의 다른 장점

	setter를 사용하는 근본적인 이유는 "객체 지향" 에서 이야기 하는 "캡슐화"를 달성하기 위해서  라고한다. private로 필드값을 숨기기고
 	만약 getter만 만들어준다면 이 클래스는 setter를 통해 값을 바꿀수 없기 때문에 getter를 통한 읽기만 가능한 클래스 가 된다. 이러한 정보 은닉이 가능하다는 큰 장점이 있다. 





### 느낀점
이펙티브 자바를 통해 심화적인 내용을 많이 공부하게 될줄 알았는데 그 심화적인 내용을 이해하기 위한 기초적인 지식들중 오늘은 setter에 대해 집중적으로 알아보았다. setter는 자바의 객체지향중 캡슐화를 가능하게 만드는 중요한 메서드이다. 또한 setter를 통한 의존성 주입으로 모듈간의 연결도 느슨하게 만들수 있게 하는 자바에서 여러가지 역할을 하고 있다는걸 배울수 있었다. 




### 참조: 
https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html

https://docs.oracle.com/en/database/oracle/oracle-database/19/jjdev/Java-overview.html#GUID-68EE1A7B-1F78-4074-AB76-AF9B2CE878F6