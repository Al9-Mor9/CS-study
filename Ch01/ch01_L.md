# 디자인 패턴과 프로그래밍 패러다임



## 디자인 패턴

디자인 패턴이란 소프트웨어 공학의 소프트웨어 디자인에서 특정 문맥에서 공통적으로 발생하는 문제에 대해 재사용 가능한 해결책. - wiki

일반적으로 3가지 유형으로 구분된다.
1. 생성패턴(Creational Patterns): 객체 생성 방법에 대한 패턴.
- 싱글톤, 팩토리, 프록시

2. 행동 패턴(Behavioral Patterns): 객체들이 상호작용하는 방법과 관련된 패턴, 객체들 간의 행동을 조직하고 관리함.
- 전략, 옵저버, 이터레이터

3. 구조 패턴(Structural Patterns): 클래스나 객체를 조합하여 더 큰 구조를 만드는 방법과 관련된 패턴, 클래스나 객체들 간의 관계를 정의함.
- 노출 모듈, MVC, MVP, MVVM




### 싱글톤 패턴

전역 변수를 사용하지 않고 객체(인스턴스)를 하나만 생성 하도록 하며, 생성된 객체를 어디에서든지 참조할 수 있도록 하는 패턴.
(최초 한번만 메모리에 할당)

하나의 인스턴스가 생성되므로 이 인스턴스를 동시에 참조할 수도 있는 멀티 스레드 환경에서 안전하지 않다. 

- race codition 위험 : 두 개 이상의 스레드가 공유자원에 접근하여 실행 순서에 따라 결과가 달라지거나, 의도하지 않은 동작이 발생하는 문제. 

-> 스레드가 동시에 공유 자원에 접근하는 것을 제어하는 적절한 동기화 매커니즘이 필요하다.


#### 코드 예시

```
public class Singleton {

    private static Singleton instance = null;
    
    private Singleton() {
        // private 생성자
    }
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

#### 멀티 스레드 환경에서 Thread-safe를 지키는 방법.

synchronized,  double-checked locking, LazyHolder 를 이용하여 Thread-safe하게 구현 할 수 있다.

synchronized는 메소드를 락할 수 있으며, synchronized 가 추가된 메소드는 단일 스레드만 참조할 수 있다.
스레드가 다른 메소드를 호출해도 synchronized 가 붙어있고, 다른 synchronized 메소드가 이미 다른 스레드에 의해 호출 되어있다면 
대기 상태에 머물러야한다.

이는 overhead로 이어진다...

#### 특징들...

- 유일한 자료로, 단 1개만 존재해야 하는 데이터 베이스 연결 모듈에 많이 사용함. (DBCP: 데이터베이스 커넥션 풀)
- 인스턴스 생성 비용 감소.
- 의존성이 높아진다는 단점.

#### 개방 폐쇄 원칙(Open closed Principle)

- 소프트웨어 확장에 대해서는 열려있어야 하지만, 변경에 대해서는 닫혀 있어야 한다.
- 추상화를 통해 코드를 분리하고, 인터페이스나 추상 클래스 등을 활용하여 구현 세부사항에 대한 의존성을 낮추는 것이 중요하다.
- 유연성과 재사용성을 높일 수 있다.


#### 의존성이 뭐지?

하나의 모듈이나 클래스가 다른 모듈이나 클래스에 의존하는 것을 말한다.


#### 의존성 주입(Dependency Injection, DI)

외부에서 객체에 필요한 의존성을 주입해주는 방식, 디자인 패턴. 

- 생성자 주입, 세터 주입, 인터페이스 주입이 있다.

- 싱글톤 패턴에 의존성 주입이 적용된 예시

```
public class MySingleton {
    private static MySingleton instance;

    private MyDependency myDependency;

    private MySingleton() {}

    public static MySingleton getInstance() {
        if (instance == null) {
            instance = new MySingleton();
        }
        return instance;
    }

    public void setMyDependency(MyDependency myDependency) {
        this.myDependency = myDependency;
    }

    public void doSomething() {
        if (myDependency == null) {
            throw new RuntimeException("Dependency not initialized");
        }
        myDependency.doSomething();
    }
}

public class MyDependency {
    public void doSomething() {
        // do something
    }
}

```

MySingleton 클래스는 MyDependency 클래스에 의존한다. 

그러나 MySingleton클래스는 MyDependency를 setMyDependency 메소드를 통해 주입받는다. -> 의존성 주입

MyDependency에 의존하면서도 MyDependency이 변경되더라도 MySingleton의 변경 없이 새로운 구현이 가능하다.



### 팩토리 

팩토리 패턴은 객체를 사용하는 코드에서 객체 생성 부분을 추상화한 패턴이다.

상속 관계의 두 클래스 중 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정.

객체의 생성을 캡슐화 한다. 객체 생성을 추상화하고, 유연성을 높일 수 있다.


#### GPT 햄이 보여준 예시

```
// 도형 클래스
public interface Shape {
    void draw();
}

// 도형 클래스를 상속받은 원 클래스
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

// 도형 클래스를 상속받은 사각형 클래스
public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }
}

// 도형 클래스를 생성하는 팩토리 클래스
public class ShapeFactory {
    // 팩토리 메소드 - 도형을 생성하고 반환
    public Shape getShape(String shapeType){
        if(shapeType == null){
            return null;
        }
        if(shapeType.equalsIgnoreCase("CIRCLE")){
            return new Circle();
        } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
            return new Rectangle();
        }
        return null;
    }
}

// 도형 클래스를 사용하는 클라이언트 클래스
public class ShapeClient {
    public static void main(String[] args) {
        // 팩토리 객체 생성
        ShapeFactory shapeFactory = new ShapeFactory();

        // 도형 생성
        Shape shape1 = shapeFactory.getShape("CIRCLE");
        Shape shape2 = shapeFactory.getShape("RECTANGLE");

        // 도형 사용
        shape1.draw();
        shape2.draw();
    }
}
```



### 프록시

프록시 패턴은 대상 객체에 접근하기 전 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 하는 디자인 패턴

- 보안성 강화
- 원격으로 객체 접근
- 객체 생성 및 초기화 시간 단축(필요한 시점에 생성)
- 객체에 대한 접근 제어

```
public interface Image {
    void display();
}

public class RealImage implements Image {
    private String fileName;
 
    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk(fileName);
    }
 
    @Override
    public void display() {
        System.out.println("Displaying " + fileName);
    }
 
    private void loadFromDisk(String fileName) {
        System.out.println("Loading " + fileName);
    }
}

public class ProxyImage implements Image {
    private RealImage realImage;
    private String fileName;
 
    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }
 
    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName);
        }
        realImage.display();
    }
}

public class ProxyPatternExample {
    public static void main(String[] args) {
        Image image = new ProxyImage("test.jpg");
        image.display();
        System.out.println("");
        image.display();
    }
}

```

원봅 객체의 생성과 초기화가 오래 걸리는 경우 적용됨.
대용량 이미지나 동영상 파일과 같은 경우 로딩이 오래 걸린다.
이 경우 처음에 로딩하지 않고 프록시 객체를 사용하여 파일을 로딩하고, 캐시해둔다.(임시 저장.)



#### 프록시

(https://www.youtube.com/watch?v=c0_JVomjN7o)


Client ----- proxy ----- Server

[Supreme]
옷 브랜드..
인기 짱 많아서 다른나라 사람은 못 사게함.


미국 프록시를 맹글어버림.
Client ----- proxy ----- Server
한국.          미국.      슈프림

슈프림은 어라라 미국에서 구매하네~ 



Client ----- proxy ----- Server
ID, PASS ----> 프록시
해커가 프록시 서버를 운영하면 위험하다! 


MITM 공격 : 중간자 공격
카페에 가면 위험해~ -> 카페 와이파이가 하나의 Gateway

와이파이쓰는 사람들은 Gateway를 통해 정보를 송수신.


- 웹 프록시 :  웹 요청을 중간에 받아 보겠다!


웹 브라우저 ----- 웹 프록시 ----- 웹 서버
                 Burp Suite? 웹 프록시 툴인듯 



### 전략

전략 패턴은 정책 패턴이라고도 하며, 객체의 행위를 바꾸고 싶은 경우 '직접' 수정하지 않고 전략(캡슐화한 알고리즘)을 컨텍스트 안에서 바꿔주면서 상초 료체가 가능하게 만드는 패턴이다.

3 가지 요소가 필요하다. 
- 전략을 정의하는 인터페이스
- 여러 전략을 구현한 클래스
- 전략을 사용하는 클라이언트


```
// 1. 전략을 정의하는 인터페이스
public interface SortingStrategy {
    public void sort(int[] arr);
}

// 2. 여러 전략을 구현하는 클래스
public class BubbleSort implements SortingStrategy {
    public void sort(int[] arr) {
        System.out.println("Bubble Sort");
        // bubble sort implementation
    }
}

public class QuickSort implements SortingStrategy {
    public void sort(int[] arr) {
        System.out.println("Quick Sort");
        // quick sort implementation
    }
}

// 3. 전략을 사용하는 클라이언트
public class SortingClient {
    private SortingStrategy strategy;

    public SortingClient(SortingStrategy strategy) {
        this.strategy = strategy;
    }

    public void setSortingStrategy(SortingStrategy strategy) {
        this.strategy = strategy;
    }

    public void sort(int[] arr) {
        strategy.sort(arr);
    }
}

```

알고리즘의 구현과 사용을 분리하여 코드를 유연하고 확장 가능하게 만들 수 있다.

팩토리 패턴 -> 객체의 생성과 사용을 분리
전략 패턴 ->  알고리즘 구현과 사용을 분리

전략패턴은 특정 작업을 수행하는 여러 알고리즘이 있을 때, 각각을 캡슐화 하고 이를 사용하는 클라이언트 코드와 분리해서 유연성을 높인다.

팩토리 패턴은 객체 생성을 추상화하여, 객체 생성 과정을 클라이언트 코드와 분리해내어 유연성을 높인다.


### 옵져버

옵저버 패턴은 주제가 어떤 객체의 상태 변화를 관찰하다가 상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에게 변화를 알려주는 디자인 패턴이다.

이벤트 기반 시스템에 사용하며 MVC(Model View Controller)패턴에도 사용된다.

객체 간의 일대다(one-to-many) 의존관계를 정의하는 디자인 패턴 중 하나로 볼 수 있다.

관찰 대상이 되는 객체인 Subject와 주체 객체의 상태 변화를 관찰하는 Observer로 구성된다.

```
public interface Observable {
    void addObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers(String message);
}

public interface Observer {
    void update(String message);
}

public class ConcreteObservable implements Observable {
    private List<Observer> observers;

    public ConcreteObservable() {
        observers = new ArrayList<>();
    }

    @Override
    public void addObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers(String message) {
        for (Observer o : observers) {
            o.update(message);
        }
    }
}

public class ConcreteObserver implements Observer {
    private String name;

    public ConcreteObserver(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " received a message: " + message);
    }
}


```

Observable 인터페이스를 구현하는 ConcreteObservable 클래스,
Observer 인터페이스를 구현하는 ConcreteObserver 클래스로 구성됨. 



### 이터레이터

이터레이터 패턴은 이터레이터를 사용하여 컬렉션의 요소에 접근하는 디자인 패턴이다.



### 노출 모듈

노출 모듈 패턴은 즉시 실행 함수를 통해 pribate, public같은 접근 제어자를 만드는 패턴이다. 

### MVC

Model, View, Controller로 구성된 디자인 패턴이다.

모델은 앱의 데이터인 데이터베이스, 상수, 변수 등을 뜻한다.
뷰는 사용자 인터페이스 요소를 나타낸다. 모델을 기반으로 사용자가 볼 수 있는 화면을 뜻한다.
컨트롤러는 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할을 한다. 이벤트 등 메인 로직을 담당한다.



### MVP

MVC패턴에서 파생되었다. Controller가 presenter로 교체된 패턴이다.



### MVVM

MVC에서 파생되었고, Controller가 view model로 바뀐 패턴이다.

view model은 view를 더 추상화한 계층이고, 커맨드와 바인딩을 가지는 것이 특징이다.

커맨드는 사용자 인터페이스의 이벤트 처리를 위한 객체. 

바인딩은 데이터와 UI요소간의 연결을 말한다. ex) 텍스트 박스에 입력한 데이터를 뷰모델에 있는 데이터와 자옫으로 연결해주는 작업.
이를 통해 UI요소와 데이터 간의 일관성을 유지할 수 있다.



## 프로그래밍 패러다임


프로그래머에게 프로그래밍의 관점을 갖게 해주는 역할을 하는 개발 방법론.


- 선언형
    - 함수형

- 명령형
    - 객체 지향형

    - 절차 지향형




### 선언형 - 함수형 프로그래밍

선언형 프로그래밍은 무엇을 풀어내는가에 집중하는 패러다임.

함수형 프로그래밍은 작은 '순수 함수'들을 블록처럼 쌓아 로직을 구현하고,

'고차 함수'를 통해 재사용성을 높인 프로그래밍 패러다임.

일급객체란 다음 조건들을 만족하는 개체를 의미
- 변수나 데이터 구조안에 저장할 수 있어야한다.
- 파라미터로 전달할 수 있어야 한다.
- 반환값으로 사용할 수 있어야 한다.
- 런타임에 생성될 수 있어야 한다.

함수형 프로그래밍에서 함수는 값으로 취급되고, 일급객체로 취급된다.

### 객체지향 프로그래밍

객체들의 집합으로 프로그램의 상호 작용을 표현하며 데이터를 객체로 취급하여 객체 내부에 선언된 메서드를 활용하는 방식

추상화 - 복잡한 시스템으로부터 핵심적인 개념 또는 기능을 간추려내는 것.
캡슐화 - 객체의 속성과 메서드를 하나로 묶고 일부를 뢰부에 감추어 은닉하는 것.
상속성 - 상위 클래스의 특성을 하위 클래스가 이어받아서 재사용하거나 추사, 확장하는 것. 재사용, 계층 관계, 유지 보수에서 중요
다형성 - 하나의 메서드나 클래스가 다양한 방법으로 종작하는 것. 오버로딩. 오버라이딩

정적 다형성 - 오버로딩 - 같은 이름을 가진 메서드를 여러 개 두는 것.
동적 다형성 - 오버라이딩 - 상위 클래스로부터 상속받은 메서드를 하위 클래스가 재정의 하는 것.


#### SOLID


SRP(Single Responsibility Principle) - 모든 클래스는 각각 하나의 책임만을 가져야함.
OCP(Open Closed Principle) - 유지 보수 사항이 생긴다면 코드를 쉽게 확장할 수 있도록 하고 수정할 때는 닫혀 있어야 하는 원칙.
LSP(Liskov Substitution Principle) - 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 하는 것.
ISP(Interface Segregation Principle) - 하나의 일반적인 인터페이스보다 구체적인 여러 개의 인터페이스를 만들어야함.
DIP(Dependency Inversion Principle) - 변하기 쉬운 것에 영향을 받지 않게 하기 위해 추상화된 인터페이스나 상위 클래스를 두어서 독립성을 향상.



### 절차형 프로그래밍

로직이 수행되어야 할 연속적인 계산 과정으로 이루어져 있다.
가독성이 좋고 실행 속도가 빠르다. 

모듈화가 어렵고 유지 보수성이 떨어짐.

대기 과학 관련 연산, 머신 러닝의 배치 작업...?


