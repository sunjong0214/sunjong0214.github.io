---
title : Factory 패턴
categories : [Java]
tags : [디자인 패턴, Factory 패턴]
---

Factory 패턴은 객체 생성 역할을 별도의 클래스(Factory)에게 위임하는 것이 궁극적인 목표이다.

디자인 패턴 중 Factory는 팩토리 메소드 패턴과 추상 팩토리 패턴 두 가지로 크게 나뉜다.

이 두 가지 패턴의 베이스가 되는 가장 단순한 형태의 Factory 패턴이 존재한다.

보통 Simply Factory라는 이름으로 많이 불린다. 먼저 Simply Factory에 대해 알아본 뒤 팩토리 메소드 패턴과 추상 팩토리 패턴에 대해 알아보자.

## Simply Factory

이름 그대로 매우 단순한 형태이다.

객체는 여러 곳에서 생성될 수 있는데, 호출하는 쪽이 객체의 생성자에 직접 의존하고 있으면 나중에 수정, 변경이 어렵다. 

그래서 생성자의 호출 (new)을 별도의 클래스 (Factory)에서 담당하고 클라이언트 코드에서는 팩토리를 통해 객체를 생성한다.

예를 통해 살펴보자.

#### Before

```java
public interface Pet {
}

public class Cat implements Pet {
}

public class Dog implements Pet {
}
```

공통 인터페이스인 Pet을 정의하고 그것을 상속 받은 Cat, Dog를 만들었다.

클라이언트 코드에서 다음과 같이 생성한다.

```java
Pet cat = new Cat();
Pet dog = new Dog();
```

하지만 이렇게 한다면 클라이언트와 클래스들 사이에 의존관계가 생긴다.

![](https://github.com/ParkJiwoon/PrivateStudy/blob/master/design-pattern/images/screen_2022_05_29_05_02_35.png?raw=true)

이렇게 구현 클래스를 직접 의존하면 해당 클래스의 생성자나 전처리 코드가 변경되었을 때 사용하는 모든 Client 코드를 변경해야한다.

그래서 객체 생성만을 담당하는 별도의 Factory 클래스를 만들어 생성 역할을 넘겨야한다.

#### After

```java
public interface Pet {
    enum Type{
        CAT, DOG
    }
}
```

- `Pet` 인터페이스 `enum`으로 타입을 선언한다.

```java
public class PetFactory {

    public Pet createPet(Pet.Type petType) {
        switch (petType) {
            case CAT:
                return new Cat();
            case DOG:
                return new Dog();
            default:
                throw new IllegalArgumentException("Pet 타입이 아닙니다");
        }
    }
}
```

- `PetFactory`를 만든 후 `Pet.Type`에 따라 다른 객체를 생성해서 반환합니다.

```java
PetFactory petFactory = new PetFactory();
Pet cat = petFactory.createPet(Pet.Type.CAT);
Pet dog = petFactory.createPet(Pet.Type.DOG);
```

- `PetFactory`를 선언한 후 생성 메소드만 호출하면 실제 구현 클래스인 `Cat`, `Dog`에 의존하지 않은 코드를 작성할 수 있다.

![](https://github.com/ParkJiwoon/PrivateStudy/blob/master/design-pattern/images/screen_2022_05_29_05_29_30.png?raw=true)

그림으로 표현하면 위와 같다.

Client에서 구현 클래스를 직접 의존하지 않기 때문에 나중에 클래스 이름이 변경되거나, 생성자가 변경되는 경우에도 PetFactory 내부만 수정하면 된다.

## Simply Factory 한계

만약 새로운 애완 동물 구현 클래스 Bird가 추가된다면, PetFactory 내부에 존재하는 switch 문에 해당 클래스를 추가해야한다.

객체지향 원칙은 확장을 할 때 기존 코드에 영향을 주지 않는 것을 지향한다.

팩토리 메서드나 추상 팩토리 패턴을 활용한다면 기존 클래스에 영향을 주지 않고 확장이 가능하다.

# Factory Method

Simply Factory에서 새로운 클래스가 추가되었을 때 Factory 클래스를 수정해야 한다는 한계가 있다.

기존 코드의 변경 없이 확장하기 위한 디자인 패턴이 Fectory Method 패턴이다.

![](https://github.com/ParkJiwoon/PrivateStudy/blob/master/design-pattern/images/screen_2022_05_27_00_20_31.png?raw=true)

Factory Method 패턴은 생성 패턴 중 하나로 객체를 생성할 때 어떤 클래스의 인스턴스를 만들지 서브 클래스에서 결정한다. 즉, 인스턴스 생성을 서브 클래스에게 위임한다.

부모 추상 클래스는 인터페이스에만 의존하고 실제로 어떤 클래스를 호출할 지는 서브 클래스에서 구현한다.

이렇게 하면 기존 코드 수정없이 새로운 구현 클래스 추가가 가능하다.

아래 예를 통해 알아보자.

사용자 관리 프로그램이 있고 네이버 계정으로 가입할 수 있다고 가정하자.

#### Product(User)

```java
public interface User {
    void signup();
}
```

- `User` 인터페이스 정의

```java
public class NaverUser implements User {
    @Override
    public void signup() {
        System.out.println("네이버 아이디로 가입	");
    }
}
```

- `User` 인터페이스를 구현하는 `NaverUser` 클래스 정의
- 오버라이드한 메서드에는 네이버 유저 전용 로직 추가

#### Creator (UserFactory)

```java
public abstract class UserFactory {
    public User newInstance() {
		User user = createUser();
        user.signup();
        return user;
    }
    protected abstract User createUser();
}
```

- 추상 클래스로 `UserFactory`를 정의
- 외부에서 `User` 객체를 생성할 때는 `newInstance()` 메소드를 호출하면 되고, 실제로 어떤 객체를 생성할 지는 추상 메서드로 정의해서 하위 클래스에서 정의
- 자식 클래스에서 사용 가능하도록 `protected` 키워드를 사용

```java
public class NaverUserFactory extends UserFactory {
    @Override
    protected User createUser() {
        return new NaverUser();
    }
}
```

- `UserFactory`를 상속받는 `NaverUserFactory`를 정의
- `NaverUser`를 반환하도록 오버라이드

#### Client

```java
UserFactory userFactory = new NaverUserFactory();
User user = userFactory.newInstance();
```

- 클라이언트 코드에서 `NaverUser` 클래스에 대한 의존성 없이 사용가능
- 의존성 주입을 사용해서 외부에서 `Factory` 클래스를 받아온다면 `NaverUserFactory`에 대한 의존성도 제거 가능



## 스펙 확장

이번에는 카카오 서비스가 오픈되고 카카오 계정으로 가입할 수 있게 확장해보자.

Simply Factory 에서는 UserFactory 코드를 수정해야하지만, Factory Method 패턴을 적용했기 때문에 기존 코드의 수정 없이 새로운 코드만 추가하면 된다.



## 새로운 Product와 Creator

```java
public class KakaoUser implements User {
    @Override
    public void signup() {
        System.out.println("카카오 아이디로 가입");
    }
}
```

- `NaverUser` 클래스와 동일하게 `User` 인터페이스를 구현하는 `KakaoUser` 클래스 추가

```java
public class KakaoUserFactory extends UserFactory {
    @Override
    protected User createUser() {
        return new KakaoUser();
    }
}
```

- `NaverUserFactory` 클래스와 동일하게 `KakaoUserFactory` 정의

```java
UserFactory userFactory = new NaverUserFactory();
User user = userFactory.newInstance();

// 위 클라이언트 코드 수정 없이 다른 곳에서 사용 가능
UserFactory kakaoUserFactory = new KakaoUserFactory();
User kakaoUser = kakaoUserFactory.newInstance();
```

- 기존 코드의 변경 없이 새로 선언한 클래스만 사용하여 확장 가능



## 장단점

- 장점 : Factory Method 패턴의 가장 큰 장점은 지금까지 본 것처럼 수정에 닫혀있고 확장에는 열려있는 OCP 원칙을 지킬 수 있다.
- 단점 : 코드량이 증가한다.



# Abstract Method

마지막으로 추상 팩토리 패턴이다.

팩토리 메소드 패턴과 비슷하지만, 팩토리 메소드 패턴은 어떤 객체를 생성 할지에 집중하고 추상 팩토리는 연관된 객체들을 모아둔다는 것에 집중한다.

![](https://github.com/ParkJiwoon/PrivateStudy/blob/master/design-pattern/images/screen_2022_05_31_04_12_38.png?raw=true)

추상 팩토리 패턴은 연관된 객체들의 생성을 하나의 팩토리에서 담당한다.

예를 들어서 알아보자.

#### ProductA (Manager)

```java
public interface Manager {
}

public class SoccerManager implements Manager {
}

public class TennisManager implements Manager {
}
```

- `Manager` 인터페이스와 클래스를 정의
- 축구팀과 테니스팀이 존재하기 때문에 두 개의 `Manager` 구현 클래스를 정의

#### ProductB (Player)

```java
public interface Player {
}

public class SoccerPlayer implements Player {
}

public class TennisPlayer implements Player {
}
```

- `Player` 인터페이스와 클래스를 정의
- 매니저와 마찬가지로 축구 선수와 테니스 선수를 정의

#### Factory (StaffFactory)

```java
public interface StaffFactory {
    Manager createManager();
    Player createPlayer();
}

public class SoccerStaffFactory implements StaffFactory {

    @Override
    public Manager createManager() {
        return new SoccerManager();
    }

    @Override
    public Player createPlayer() {
        return new SoccerPlayer();
    }
}

public class TennisStaffFactory implements StaffFactory {

    @Override
    public Manager createManager() {
        return new TennisManager();
    }

    @Override
    public Player createPlayer() {
        return new TennisPlayer();
    }
}
```

- Product 를 생성하는 Factory 클래스를 정의
- `Manager`, `Player` 는 축구라는 하나의 공통점으로 묶을 수 있다.
- 그래서 `SoccerManager`, `SoccerPlayer` 를 생산하는 `SoccerStaffFactory` 와 반대로 테니스 객체들을 생성하는 `TennisStaffFactory` 를 정의
- 단순하게 생각하면 팩토리 메서드 패턴과 동일하지만 **공통된 집합을 모아둔다**는 점이 특징

#### Client

```java
public class AbstractFactoryApp {
    public static void main(String[] args) {
        use(new SoccerStaffFactory());
        use(new TennisStaffFactory());
    }

    private static void use(StaffFactory factory) {
        Manager manager = factory.createManager();
        Player player = factory.createPlayer();
    }
}
```

- 구체 클래스가 아닌 인터페이스에 의존하게 작성할 수 있다.

- 어떤 Factory 를 넘겨받는지에 관계 없이 클라이언트는 `Manager`, `Player` 를 생성해서 사용할 수 있다.

#### 의존 관계 다이어그램

![](https://github.com/ParkJiwoon/PrivateStudy/blob/master/design-pattern/images/screen_2022_06_02_01_56_33.png?raw=true)

## 장단점

- 장점
  - 팩토리 메서드 패턴과 마찬가지로 수정에는 닫혀 있고 확장에는 열려 있다.
  - 여려 개의 비슷한 집합 객체 생성을 하나의 팩토리에 모아둘 수 있다.
- 단점
  - 팩토리 메서드와 마찬가지로 클래스 갯수가 늘어난다.



출처) 

[Simply Factory](https://bcp0109.tistory.com/366)

[Factory Method](https://bcp0109.tistory.com/367)

[Abstract Factory](https://bcp0109.tistory.com/368)