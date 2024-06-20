## @Override 적용 여부의 차이에 대해

### 정리

**내용 요약**

상위 클래스를 상속받거나, 추상클래스 또는 인터페이스를 구현할 때 디폴트 메서드를 재정의 하는 경우 @Override 애너테이션을 적용하여 재정의를 명시하는 것이 좋다.   
@Override 애너테이션을 적용하지 않은 경우, 시그니처의 차이점을 발견하지 못한다면 재정의가 아니라 단순히 이름만 같은 새로운 메서드가 될 수도 있다.  
메서드 재정의에 오류가 없도록 방지하기 위해서는 @Override 애너테이션을 일관되게 사용해야 한다.  


### 심화 탐구

**출발점**
 
애너테이션을 사용하면 컴파일러가 시그니처 오류를 지적하기 때문에 실수로 의도에 맞게 메서드를 재정의할 수 있다.  
전반적으로 이해가 어려운 내용은 아니지만, 저자가 예시로 들었던 equals, hashCode 메서드를 사용해 비슷하면서도 간단한 테스트를 직접 수행해보았다.  

**설명**

<hr>

1. Person 클래스 정의

    먼저, 필드로 name과 birth 값을 갖는 Person 클래스를 정의했다.  
    이때 equals과 hashCode 메서드 구현은 아래와 같으며 @Override를 적용하지 않고 equals의 시그니처를 달리했다.

    ```java
    public class Person {
	
        private String name;
        private int birth;
        
        public Person(String name, int birth) {
            this.name = name;
            this.birth = birth;
        }
        
        // 이름의 길이와 생년월일이 같다면 같은 객체로 인식하도록 임시로 정했다.
        public int hashCode() {
            return name.length()*birth;
        }
        
        // Object의 equals 메서드를 재정의 하려면 매개변수의 타입은 Object여야 한다.
        public boolean equals(Person p) {
            return p.name.equals(name);
        }
        
    }
    ```

    Test main함수에 Set<Person>을 통해 같은 이름과 생년월일의 사람을 추가해봤다.

    ```java
    public class Test {
        public static void main(String[] args) {

            Person p1 = new Person("육예진", 970418);
            Person p2 = new Person("육예진", 970418);
            
            Set<Person> set = new HashSet<>();
            set.add(p1);
            set.add(p2);
            
            System.out.println(set);

            // [Java_Study.Person@2c6c16, Java_Study.Person@2c6c16] 출력
        }
    }
    ```

    출력되는 해시코드의 값은 같아보이지만, Set 자료구조에서 두 객체가 같은 객체임을 인식하지 못해 중복제거가 되지 않았다.  

2. @Override 애너테이션을 추가해봤다.

    ```java
    @Override
	public int hashCode() {
		return name.length()*birth;
	}
	
	@Override
	public boolean equals(Person p) {   // <<< 오류 뜸
		return p.name.equals(name);
	}
    ```
    @Override 애너테이션을 적용하니 표시한 부분에 `The method equals(Person) of type Person must override or implement a supertype method`라는 오류 메시지가 떴다.   
    해당 메서드를 재정의 하려면 type이 Object여야 한다는 것이다.  

    따라서 다음과 같이 코드를 수정했다.

    ```java
    @Override
	public boolean equals(Object o) {
		Person p = (Person) o;
		return p.name.equals(name);
	}
    ```
    @Override 애너테이션을 정상적으로 적용한 뒤 Test를 다시 실행해보니, 중복제거가 잘 되어 원소가 1개만 출력되었다.


**결론**
    
이후 추가 테스트를 통해 @Override 애너테이션을 적용하지 않더라도 시그니처가 같으면 상위 메서드를 정상적으로 재정의 하는 것을 확인했다.  
그러나, 애너테이션이 없는 경우 컴파일러는 개발자의 의도를 알 수 없기에 재정의하려는 의도라면 일관적으로 애너테이션을 사용하는 것이 가독성에서도 오류방지 차원에서도 도움이 될 것이다.

<hr>

