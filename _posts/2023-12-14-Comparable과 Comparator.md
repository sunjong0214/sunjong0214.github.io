기본적으로 Comparable과 Comparator는 모두 인터페이스(Interface)다.

즉, Comparable과 Comparator 모두 인터페이스 내에 선언된 메소드를 '반드시 구현' 해야한다.

그럼 어떤 메소드를 구현해야 하는지 각 API문서를 뒤져보자. 

Comparable 인터페이스에는 compareTo(T o) 메소드가 하나 선언 되어있다. 

Comparator 인터페이스에는 compare(T o1, T o2) 메소드가 선언 되어있다.

그 말은 우리가 두 인터페이스를 사용하기 위해서는 각각 compareTo(T o) 메소드와 compare(T o1, T o2) 메서드를 구현해야 한다.

이제 이 두 인터페이스의 사용 방법에 대한 기본적인 정보를 찾았으니 본격적으로 이 차이와 사용방법에 대해 알아보자.

## Comparable과 Comparator

두 인터페이스는 보통 객체를 정렬하기 위해 쓴다고 생각하지만, 정확히는 그 건 용도에 불과하다.

정확한 역할은 "객체를 비교할 수 있도록 만든다." 이다.

우리는 primitive 타입인 기본 자료형들은 쉽게 부등호를 가지고 비교할 수 있다는 것을 알고 있다. 

하지만 클래스 객체를 만들어 비교를 해야한다고 생각하면 막막하다고 느낄 것이다.

만약 우리가 학생의 나이와 학급 정보를 갖고 있는 클래스를 만든다고 가정하자.

```java
public class Test {
	public static void main(String[] args)  {
 
		Student a = new Student(17, 2);	// 17살 2반
		Student b = new Student(18, 1);	// 18살 1반
		
		/*
		   어떻게 비교..?
		   
		   if(a > b) ..?
		 */
		
	}
}

class Student {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
}
```

a 학생과 b 학생 객체를 생성했다.

만약 두 학생을 비교해야한다면 나이를 기준으로 잡아야할까? 아니면 학급을 기준으로 잡아야할까?

이 부분이 우리가 Comparable과 Comparator을 쓰는 이유이다. 

사용자마다 나이를 기준으로 잡고 싶은 사람이 있을 것이고, 어떤 사람은 학급을 기준으로 잡고 싶을 것이다. 그렇기 때문에 사용자가 어떤 부분을 더 높은 우선순위로 생각하는지 "기준"을 정하게 해주는 것이다.



그럼 이제 두 인터페이스의 차이점에 대해 알아보자.

왜 Comparable의 compareTo(T o) 메소드는 파라미터(매개변수)가 한 개이고, Comparator의 compare(T o1, T o2) 메소드는 파라미터가 왜 두 개인 것 일까?

그 답은 Comparable은 "자기 자신과 매개변수 객체를 비교" 하는 것이고, Comparator은 "두 매개 변수 객체를 비교"하는 것 이기 떄문이다.

또 다른 차이점이 있다면 Comparable은 lang패키지에 있기 때문에 import 를 해줄 필요가 없지만, Comparator는 util패키지에 있다.

이제 두 인터페이스에 대해 자세히 알아보자.

## Comparable

Comparable은 "자기 자신과 매개변수 객체를 비교"한다고 했다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbgjmaA%2Fbtq3Fnxx3RH%2F7zsHFMkhuuH7pcdj3GJUGK%2Fimg.png)

Comparable 인터페이스는 위와 같이 정의 되어있다. 보다시피 제네릭 타입을 받는 다는 것을 알 수 있다.

```java
public class ClassName implements Comparable<Type> { 
 
/*
  ...
  code
  ...
 */
 
	// 필수 구현 부분
	@Override
	public int compareTo(Type o) {
		/*
		 비교 구현
		 */
	}
}
```

기본적인 사용법은 위와 같다. 이 때 필수 구현 부분인 compareTo() 메소드 안에 우리가 객체를 비교할 때 기준으로 삼을 부분을 정의해주면 된다.

아까 Comparable은 자기 자신과 매개변수 객체를 비교한다고 했다. 즉, 자기자신은 ClassName으로 생성한 객체 자신이 되고, 매개변수 객체는 ClassName.compareTo(o); 를 통해 들어온 파라미터 o가 비교 할 객체가 되는 것이다.



예를 들어, 아까 Student 클래스를 다시 사용해 어떤 식으로 활용되는지 알아보자.

일단, Student 클래스에 Comparable 을 implements 해야한다. 그리고 <> 사이에 들어갈 타입은 무엇일까? **Student 객체와 또 다른 Student 객체를 비교**하고 싶다면, <> 사이에 들어갈 타입 또한 Student가 되어야하지 않겠는가? 즉, Type 은 Student로 바뀌게 된다.

```java
class Student implements Comparable<Student> {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
	
	@Override
	public int compareTo(Student o) {
		/*
		 * 비교 구현
		 */
	}
}
```

이제 compareTo 메소드를 구현해야 할 것이다. 만약 나이를 기준으로 비교(대소 관계)를 하고자 한다면 어떻게 하면 될까?

자기 자신의 age(나이)와 매개변수로 들어온 o의 age(나이)의 값을 비교하면 된다.

```java
class Student implements Comparable<Student> {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
	
	@Override
	public int compareTo(Student o) {
    
		// 자기자신의 age가 o의 age보다 크다면 양수
		if(this.age > o.age) {
			return 1;
		}
		// 자기 자신의 age와 o의 age가 같다면 0
		else if(this.age == o.age) {
			return 0;
		}
		// 자기 자신의 age가 o의 age보다 작다면 음수
		else {
			return -1;
		}
	}
}
```

compareTo 메소드를 보면 int값을 반환하도록 되어있다.

즉, 쉽게 말해 우리는 **'값'을 비교해서 정수를 반환**해야 한다는 것이다. 그럼 이러한 의문이 나올 것이다. 무슨 기준으로 양수, 0, 음수를 반환하는 건가요?

한 번 생각해보자. 우리는 "자기 자신"과 "상대방"을 비교하는 것이다. 즉, **자기 자신을 기준으로 삼아 대소관계를 파악**해야 한다.

만약 내가 갖고 있는 값이 7라고 가정해보자. 그리고 상대방은 3이라고 가정한다면, 나 자신은 상대방보다 값이 4만큼 크다.

반대로 상대방이 9을 갖고 있다고 가정하면, 나는 상대방보다 2만큼 작다. 즉, -2 만큼 크다는 것이다.

한 마디로 자기 자신을 기준으로 상대방과 얼마나 차이가 나느냐를 보는 것이다.

![](https://blog.kakaocdn.net/dn/dFBJmY/btq3GDVvbb2/3escGKzhPmC9ALssfLkkh1/img.png)

위 코드에서 리턴 값은 1,0,-1을 반환했지만, 주석으로 양수, 음수라고 표현한 것도 이 부분 때문이다.

사실 **조건문을 통해 <, >, == 을 활용하여 대소비교를 하고 그에 따라 1, 0, -1을 반환 하는 방식이 이해하기도 쉬울테고 가장 정석적인 방법**이다. 왜 정석적인 방법인지는 후에 알게 될 것이다.

아마 많은 분들도 1, 0, -1 을 반환값으로 썼거나 그렇게 배웠을 것이다. 하지만, 꼭 1, 0, -1 이 아니라 양수, 0, 음수로 표현해도 된다는 것이다.

Comparable의 compareTo는 자기 자신과 매개변수를 비교한다고 했고, compareTo는 정수를 반환하며, **자기 자신을 기준으로 상대방과의 차이 값을 비교하여 반환**한다고 했다.

이를 좀 더 생각해보면 -1, 0, 1로 반환할 수도 있으나, 그냥 두 비교대상의 값 차이를 반환해도 되지 않겠는가?

```java
class Student implements Comparable<Student> {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
	
	@Override
	public int compareTo(Student o) {
 
		/*
		 * 만약 자신의 age가 o의 age보다 크다면 양수가 반환 될 것이고,
		 * 같다면 0을, 작다면 음수를 반환할 것이다.
		 */
		return this.age - o.age;
	}
}
```

위와같이 두 값의 차를 반환해버리면 번거로운 조건식 없이 한방에 3개의 조건을 만족할 수 있다.

위 코드에서는 **this는 a객체 자신을 의미**하고, **o는 b객체를 의미**하게 된다.



#### **"단 주의해야 할 점이 있다" ( 절대 강조 )**

사실 우리가 편리하게 두 수의 대소비교를 두 수의 차를 통해 음수, 0, 양수로 구분하여 구했지만, 여기에는 치명적인 단점이 있다. 바로 뺄셈 과정에서 자료형의 범위를 넘어버리는 경우가 발생할 수 있기 때문이다.

자료형이 넘어가는 경우는 overflow가 발생한다. int 형을 예로 들자면, int 형의 범위는 -2,147,483,648 ~ 2,147,483,647 이다.

만약 -2,147,483,648 - 1 이나 2,147,483,647 + 1 과 같은 연산이 들어가면 어떻게 될까?

-2,147,483,648 - 1 = = -2,147,483,649 로 범위를 넘어가면서 int 자료형에서 표현할 수 없는 수로 +2,147,483,647로 변환되며 최대값으로 반환된다. 반대의 경우에도 2,147,483,647 + 1 = 2,147,483,648 로 범위가 넘어가며 최솟값인 -2,147,483,648이 반환된다. 이것을 Overflow라고 한다.

그렇기 때문에 우리가 위의 예시에서 사용했던 return o1 - o2; 형식을 사용했을 때 1 - (-2,147,483,648) 일 때 overflow가 일어나면서 음수 값이 나오기 때문 1인 o1이 -2,147,483,648인 o2보다 작다는 상황이 나와버린다.

그렇기 때문에 여러분이 compareTo를 구현하거나, 이후 설명 할 compare을 구현 할 때 대소비교에 있어 이러한 Overflow가 발생할 여지가 있는지를 반드시 확인하고 사용해야 한다.

특히 **primitive 값에 대해 위와 같은 예외를 만약 확인하기 어렵다면 <, >, == 으로 대소비교를 해주는 것이 안전**하며 일반적으로 권장되는 방식이다.

## Comparator

Comparator은  **"두 매개변수 객체를 비교"**한다.

이 말은 자기 자신이 아니라 파라미터(매개 변수)로 들어오는 두 객체를 비교하는 것이다. 여기서 바로 Comparable과 차이가 발생하는 것이다.

Comparator은 아래와 같은 형식을 가지고 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmRnwS%2Fbtq3I6v4eJI%2FvBBJKd7NYi5ravCCJGGqs0%2Fimg.png)

기본적인 사용 방법은 이렇다.

```java
import java.util.Comparator;	// import 필요
public class ClassName implements Comparator<Type> { 
 
/*
  ...
  code
  ...
 */
 
	// 필수 구현 부분
	@Override
	public int compare(Type o1, Type o2) {
		/*
		 비교 구현
		 */
	}
}
```

이 때, 필수 구현 부분인 **compare() 메소드가 바로 우리가 객체를 비교할 기준을 정의해주는 부분**이 된다. 앞서 말했듯, Comparable과 다르게 Comparator는 매개변수로 들어오는 두 객체를 비교하는 것이기 때문에 당연히 매개변수가 두 개가 되는 것이다.



Student를 이용해 이번엔 학급을 기준으로 적용해보자.

```java
import java.util.Comparator;	// import 필요
class Student implements Comparator<Student> {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
	
	@Override
	public int compare(Student o1, Student o2) {
    
		// o1의 학급이 o2의 학급보다 크다면 양수
		if(o1.classNumber > o2.classNumber) {
			return 1;
		}
		// o1의 학급이 o2의 학급과 같다면 0
		else if(o1.classNumber == o2.classNumber) {
			return 0;
		}
		// o1의 학급이 o2의 학급보다 작다면 음수
		else {
			return -1;
		}
	}
}
```

앞서 Comparable의 compareTo()와는 다르게, 두 객체를 비교하는 것이기 때문에 파라미터로 들어오는 o1과 o2의 classNumber을 비교해주는 것이다.

좀 더 구체적으로 말하자면 Comparable의 compareTo는 선행 원소가 자기 자신이 되고, 후행 원소가 매개 변수로 들어오는 o 가 되는 반면에, Comparator의 compare는 선행 원소가 o1이 되고, 후행 원소가 o2가 된다.

```java
import java.util.Comparator;	// import 필요
class Student implements Comparator<Student> {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
	
	@Override
	public int compare(Student o1, Student o2) {
 
		/*
		 * 만약 o1의 classNumber가 o2의 classNumber보다 크다면 양수가 반환 될 것이고,
		 * 같다면 0을, 작다면 음수를 반환할 것이다.
		 */
		return o1.classNumber - o2.classNumber;
	}
}
```

이와 같이 조건문을 간단하게 작성할 수 있다.

보다시피 a객체의 compare 메소드를 통해 비교하지만, 그 내부에선 두 매개변수인 b(o1)과 c(o2) 가 비교되는 것이기 때문에 a객체와는 관련 없이 두 객체의 비교 값을 반환하게 되는 것이다.

만약에 a.compare 메소드에서 a와 비교하고 싶다면 다음과 같이 해주면 되는 것이다.

**a.compare(a, b);** 

즉, 객체 자체와는 상관 없이 독립적으로 매개변수로 넘겨진 두 객체를 비교하는 것이 포인트다.

마찬가지로, Overflow에 조심하며 두 객체를 비교하자.

## Comparator 활용

comparator를 통해 compare 메소드를 사용하는 경우 사용을 위한 객체가 반드시 필요하다.

어떤 뜻이냐면, a,b,c 객체가 있다고 할 때 3 객체 중 한 객체를 통해 compare 메소드를 사용해야 한다는 의미이다.

```java
public class Test {
	public static void main(String[] args)  {
 
		Student a = new Student(17, 2);	// 17살 2반
		Student b = new Student(18, 1);	// 18살 1반
		Student c = new Student(15, 3);	// 15살 3반
			
		//          ⋁
		int isBig = a.compare(a, b);
        //           ⋁
		int isBig2 = a.compare(b, c);
		//           ⋁
		int isBig3 = b.compare(a, c);
		
	}
}
 
// Student class 생략
```


보면 메소드를 호출하기 위한 대상(⋁ 표시 된 부분)은 사실 a이건, b이건, c이건 어떤 객체를 통해 호출하던 상관이 없다.

이 말을 조금 돌려서 생각해보면, 일관성이 떨어진다는 것이다.

다른 방법으로는 비교만을 위한 객체를 하나 생성하는 방법도 있다.

```java
public class Test {
	public static void main(String[] args)  {
 
		Student a = new Student(17, 2);		// 17살 2반
		Student b = new Student(18, 1);		// 18살 1반
		Student c = new Student(15, 3);		// 15살 3반
		Student comp = new Student(0, 0);	// 비교만을 위해 사용할 객체
			
		//           ⋁
		int isBig = comp.compare(a, b);
        //            ⋁
		int isBig2 = comp.compare(b, c);
		//            ⋁
		int isBig3 = comp.compare(a, c);
		
	}
}
 
// Student class 생략
```

하지만 위 처럼 하면 우리가 Student클래스에서 변수로 두고 있던 age와 classNumber 변수는 굳이 쓸모가 없음에도 생성이 되어버린다는 단점이 있다.

즉, 우리는 Comparator의 비교 기능만 따로 가지고 있는 객체가 필요하다는 것을 알 수 있다.

이럴 때 쓸 수 있는 것이 **"익명 객체(클래스)를 활용"**하는 것이다.



익명 객체란, 쉽게 말해 '이름이 정의되지 않은 객체'를 의미한다.

자바에서는 어떠한 객체를 만든다고 한다면 class를 생성하고 이름을 정의한다.

그럼 이름이 정의되지 않다는 것은 어떤 의미일까?

우리가 클래스를 생성할 때 class 키워드 다음에 이름을 정의했다. 하지만, 이름 없이 class를 정의할 수 있는가? 불가능 하다.

 

하지만, 우리의 고민처럼 특정 구현 부분만 따로 사용한다거나, 부분적으로 기능을 일시적으로 바꿔야 할 경우가 생길 때가 있다. 이럴 때 사용할 수 있는 것이 바로 익명객체인데, 일단 코드를 먼저 보도록 하자.

```java
public class Anonymous {
	public static void main(String[] args) {
	
		Rectangle a = new Rectangle();
		
		// 익명 객체 1 
		Rectangle anonymous1 = new Rectangle() {
		
			@Override
			int get() {
				return width;
			}
		};
		
		System.out.println(a.get());
		System.out.println(anonymous1.get());
		System.out.println(anonymous2.get());
	}
	
	// 익명 객체 2
	static Rectangle anonymous2 = new Rectangle() {
		
		int depth = 30;
		@Override
		int get() {
			return width * height * depth;
		}
	};
}
 
class Rectangle {
	
	int width = 10;
	int height = 20;
	
	int get() {	
		return height;
	}
}
```


보면 우리가 일반적으로 객체 생성방식과는 조금 다르다.

 

보통의 경우 다음과 같이 생성할 것이다.

Rectangle a = new Rectangle();

 

하지만, 익명객체의 경우는 다음과 같이 생성된다.

Rectangle a = new Rectangle() { //...구현부...// };

여기서 {} 블록 안의 구현부에 대해 알면 왜 익명 객체인 것인지 알 수 있다.



우리가 객체를 구현한다는 것은 무엇일까? 바로 변수를 선언하고, 메소드를 정의하며 하나의 클래스(객체)로 만든다는 것을 의미한다. 

말 자체는 어렵지만 쉽게 생각해보면 위 Rectangle 클래스처럼 일반적인 클래스 구현 방식과, interface 클래스를 implements 하여 interface의 메소드를 재정의하거나, class 를 상속(extends)하여 부모의 메소드, 필드를 사용 또는 재정의 하는 것들 모두 객체를 구현하는 것이다.

이 때, 구현을 하는 클래스들은 모두 '이름'이 존재한다. 

그러나 한 번 **Rectangle anonymous2 = new Rectangle() {...}** 이 부분을 한 번 봐보자. 구현부에서 분명히 변수를 선언하기도 하고, Rectangle 클래스의 메소드 get()을 '재정의(Override)'를 했다.

 

즉, 쉽게 생각하여 **'Rectangle을 상속받은 하나의 새로운 class라는 것이다.'** 

분명 새로운 class인데 이름이 정의되지 않고 있다. 

이는 annoymous1 객체 또한 마찬가지다.



음? 이름은 Rectangle이 아닌가요? 라고 생각할 수 있지만 아니다. 한 번 두 코드를 비교해보자.

```java
public class Anonymous {
	public static void main(String[] args) {
 
		Rectangle a = new Rectangle();
		ChildRectangle child = new ChildRectangle();
 
		System.out.println(a.get());		// 20
		System.out.println(child.get());	// 10 * 20 * 40
	}
}
 
class ChildRectangle extends Rectangle {
	
	int depth = 40;
	
	@Override
	int get() {
		return width * height * depth;
	}
}
 
class Rectangle {
 
	int width = 10;
	int height = 20;
 
	int get() {
		return height;
	}
}
```


위 코드는 Rectangle 이라는 클래스를 상속받아 ChildeRectangle 이라는 이름으로 정의 된 자식 클래스가 있다. 그리고 그 자식 클래스에서는 depth란 필드(변수)도 새로 생성했고, get() 메소드를 가로 세로 높이의 곱을 반환하도록 재정의되었다.

그리고 각 클래스는 a와 child 란 변수 명으로 객체를 담고 있다.



그 다음 익명 객체를 사용한 코드를 한 번 보자.

```java
public class Anonymous {
	public static void main(String[] args) {
 
		Rectangle a = new Rectangle();
 
		Rectangle anonymous = new Rectangle() {
			int depth = 40;
			@Override
			int get() {
				return width * height * depth;
			}
		};
 
		System.out.println(a.get());			// 20 
		System.out.println(anonymous.get());	// 10 * 20 * 40
	}
}
class Rectangle {
 
	int width = 10;
	int height = 20;
 
	int get() {
		return height;
	}
}
```

분명 앞서 본 상속받아 ChildRectangle 클래스를 만든 것과 같지만, 이 코드는 이름이 정의되어있지 않고, anonymous라는 이름으로 객체만 생성되어 있다.

이렇게 클래스 이름으로 정의되지 않은 객체를 바로 '익명 객체'라고 한다.

이는 거꾸로 말하면, 이름이 정의되지 않기 때문에 특정 타입이 존재하는 것이 아니기 때문에 반드시 익명 객체의 경우는 상속할 대상이 있어야 한다는 것이다. 

이 때, 상속이라 함은 class의 extends 뿐만 아니라 interface의 implements 또한 마찬가지다.

```java
public class Anonymous {
	public static void main(String[] args) {
 
		Rectangle a = new Rectangle();
		
		Shape anonymous = new Shape() {
			int depth = 40;
			
			@Override
			public int get() {
				return width * height * depth;
			}
		};
 
		System.out.println(a.get());			// Shape 인터페이스를 구현한 Rectangle
		System.out.println(anonymous.get());	// Shape 인터페이스를 구현한 익명 객체
	}
 
}
 
class Rectangle implements Shape {
	int depth = 40;
	
	@Override
	public int get() {
		return width * height * depth;
	}
}
 
interface Shape {
 
	int width = 10;
	int height = 20;
 
	int get();
}
```

이제 Comparator의 compare에 익명 객체를 적용시켜보자.

분명히 Comparator라는 interface는 존재한다. 이는 구현(상속)할 대상이 존재한다는 것이다. 이는 익명객체로 만들 수 있다는 것이다.

즉, 이름은 정의 되지 않지만, Comparator을 구현하는 익명객체를 생성하면 되는 것이다.

이 때, Comparator 구현은 이 전에 class Student implements Comparator { ... } 에서 구현했던 방식을 그대로 차용하면 된다.

(단 오버플로는 따로 고려하지 않았으니 주의하시길 바란다.)

```java
import java.util.Comparator;
 
public class Test {
	public static void main(String[] args) {
    
		// 익명 객체 구현방법 1
		Comparator<Student> comp1 = new Comparator<Student>() {
			@Override
			public int compare(Student o1, Student o2) {
				return o1.classNumber - o2.classNumber;
			}
		};
	}
 
	// 익명 객체 구현 2
	public static Comparator<Student> comp2 = new Comparator<Student>() {
		@Override
		public int compare(Student o1, Student o2) {
			return o1.classNumber - o2.classNumber;
		}
	};
}
 
 
// 외부에서 익명 객체로 Comparator가 생성되기 때문에 클래스에서 Comparator을 구현 할 필요가 없어진다.
class Student {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
 
}
```

익명 객체의 경우 필요에 따라 main함수 밖에 정적(static) 타입으로 선언해도 되고, main안에 지역변수처럼 non-static으로 생성해도 된다.

외부에서 Comparator을 구현하는 익명객체가 생성되었기 때문에, Student 클래스 내부에서 우린 Comparator을 구현해줄 필요가 없어졌다.

즉, 이 전에 a.compare(b, c) 이런식이 아니라, 위에서 생성한 익명객체를 가리키는 comp 를 통해 comp.compare(b, c) 이런 식으로 해주면 된다는 것이다.

```java
public class Test {
    public static void main (String[] args) {
        Student a = new Student(14, 1);
        Student b = new Student(15, 2);
        Student c = new Student(12, 3);
        
        // 첫번째 익명 객체 사용
        int isBig = comp.compare(b, c);
        
        if (isBig > 0) {
            System.out.println("b객체가 c객체보다 큼");
        } else if (isBig == 0) {
            System.out.println("b객체가 c객체가 같다");
        } else {
            System.out.println("b객체가 c객체보다 작음");
        }
        
        // 두번째 익명 객체 사용
        int isBig = comp2.compare(b, c);
        
        if (isBig > 0) {
            System.out.println("b객체가 c객체보다 큼");
        } else if (isBig == 0) {
            System.out.println("b객체가 c객체가 같다");
        } else {
            System.out.println("b객체가 c객체보다 작음");
        }
    }
    // 익명 객체 구현 1
	public static Comparator<Student> comp = new Comparator<Student>() {
		@Override
		public int compare(Student o1, Student o2) {
			return o1.classNumber - o2.classNumber;
		}
	};
    
    // 익명 객체 구현 2
    public static Comparator<Student> comp2 = new Comparator<Student>() {
        @Override
        public int compare(Student o1, Student o2) {
            return o1.age - o2.age;
        }
    }
}
class Student {
 
	int age;			// 나이
	int classNumber;	// 학급
	
	Student(int age, int classNumber) {
		this.age = age;
		this.classNumber = classNumber;
	}
}
```

**결과**

```java
b객체가 c객체보다 작음
b객체가 c객체보다 큼
```

이런 식으로 익명 객체를 이용해 Comparator의 compare 메소드를 사용할 수 있다.

또, 위 예제와 같이  익명 객체는 이름이 정의되지 않은 하나의 새로운 클래스와 같다고 보면 되므로 이름만 다르면 몇 개던 여러개를 생성할 수 있다.

즉, 익명객체를 통해 여러가지 비교 기준을 정의할 수 있다는 것이 큰 장점인 것이다.



그럼 왜 Comparator에서의 익명 객체는 설명 했는데 Comparable에서의 익명 객체는 설명을 안 해주나요? 라는 의문이 생길 수도 있다.

그 답은 '굳이 쓸 필요가 없다.'이다.

Comparable의 경우 자기자신과 매개변수를 비교한다고 했다. 그렇다면 익명객체를 통해 Comparable을 구현해보자.

```java
public class Test {
	public static void main(String[] args)  {
 
		Student a = new Student(17, 2);	// 17살 2반
		Student b = new Student(18, 1);	// 18살 1반
        
		/*
		 * Stduent b 객체는 comp의 30이랑 비교되는 것이다.
		 * 즉, a.compareTo(b) 처럼 서로 다른 객체에 대한 비교가 불가능하다.
		 */
		int classBig = comp.compareTo(b);
        
		
	}
	
	// 학급 대소 비교 익명 객체
	public static Comparable<Student> comp = new Comparable<Student>() {
		int a = 30;
		@Override
		public int compareTo(Student o) {
			return a - o.classNumber;
		}
	};
}
// Student 클래스 생략
```

위와 같이 작성할 수 있다.

하지만 우리가 Comparator에서 익명 객체를 사용한 이유에 대해 다시 한번 떠올리자. '비교할 때 쓰이는 객체를 익명 객체로 대신하기 위해서' 라고 할 수 있다.

그런데 자기 자신과 비교하는 Comparable의 경우에는 비교할 때 쓰이는 객체는 필요하지 않다.

왜냐면 자기 자신과 그 객체를 비교하기 때문이다.

이러한 이유 때문에 Comparable에서는 익명 객체를 쓸 필요가 없다.

## Comparable, Comparator 와 정렬의 관계

이제 Comparable과 Comparator의 각각의 차이점과 사용 방법을 이해했을 것이다.

객체를 비교하기 위해 Comparable 또는 Comparator을 쓴다는 것은 곧 사용자가 정의한 기준을 토대로 비교를 하여 양수, 0, 음수 중 하나가 반환된다는 것이다.

여기서 정렬과의 관계를 알아보기 전에 Java의 일반적인 정렬기준에 대해 알고가야 할 필요가 있다.

대부분의 언어도 마찬가지지만, **Java에서의 정렬은 특별한 정의가 되어있지 않는 한 '오름차순'을 기준**으로 한다.

우리가 흔히 쓰는 Arrays.sort(), Collections.sort() 모두 오름차순을 기준으로 정렬이 된다는 것이다.

**[두 수의 비교 결과에 따른 작동 방식]**

**음수**일 경우 : 두 원소의 위치를 **교환 안함**

**양수**일 경우 : 두 원소의 위치를 **교환 함**



예를 들어, 객체 배열을 정렬하는 코드를 작성해보자.

```java
public class Test {
	
	public static void main(String[] args) {
		
		MyInteger[] arr = new MyInteger[10];
		
		// 객체 배열 초기화 (랜덤 값으로) 
		for(int i = 0; i < 10; i++) {
			arr[i] = new MyInteger((int)(Math.random() * 100));
		}
	}
	
}
 
class MyInteger {
	int value;
	
	public MyInteger(int value) {
		this.value = value;
	}
}
```

위 코드에서는 아직 정렬의 기준에 대해 작성한 부분이 없다.

여기에 우리가 앞서 배웠던, Comparator 혹은 Comparable을 사용해 정렬 기준을 정해주면 된다.

Comparable 을 사용한다면 다음과 같이 MyInteger 클래스에 구현(implements)을 해서 작성한다.

```java
class MyInteger implements Comparable<MyInteger> {
	int value;
	
	public MyInteger(int value) {
		this.value = value;
	}
 
	// 자기 자신의 value을 기준으로 파라미터 값과의 차이를 반환한다.
	@Override
	public int compareTo(MyInteger o) {
		return this.value - o.value;
	}
	
}
```

그리고 나서 정렬 메소드로 가장 많이 쓰이는 Arrays.sort() 메소드를 사용해보자.

```java
import java.util.Arrays;
 
public class Test {
	
	public static void main(String[] args) {
		
		MyInteger[] arr = new MyInteger[10];
		
		// 객체 배열 초기화 (랜덤 값으로) 
		for(int i = 0; i < 10; i++) {
			arr[i] = new MyInteger((int)(Math.random() * 100));
		}
 
		// 정렬 이전
		System.out.print("정렬 전 : ");
		for(int i = 0; i < 10; i++) {
			System.out.print(arr[i].value + " ");
		}
		System.out.println();
		
		Arrays.sort(arr);
        
		// 정렬 이후
		System.out.print("정렬 후 : ");
		for(int i = 0; i < 10; i++) {
			System.out.print(arr[i].value + " ");
		}
		System.out.println();
	}
	
}
 
class MyInteger implements Comparable<MyInteger> {
	int value;
	
	public MyInteger(int value) {
		this.value = value;
	}
	
	@Override
	public int compareTo(MyInteger o) {
		return this.value - o.value;
	}
	
}
```

이와 같이 쓰면 된다.

만약 Comparable을 구현하지 않고 그냥 작성 시 오류가 발생한다.



다음으로는 Comparator을 구현해 작성해보자.

앞서 배운 것처럼 익명 객체를 생성하여 MyInteger에 대한 Comparator를 구현해주자.

```java
Comparator<MyInteger> comp = new Comparator<MyInteger>() {
		
	@Override
	public int compare(MyInteger o1, MyInteger o2) {
		return o1.value - o2.value;
	}
};
```

아래는 적용한 코드이다.

```java
import java.util.Arrays;
import java.util.Comparator;
 
public class Test {
	
	public static void main(String[] args) {
		
		MyInteger[] arr = new MyInteger[10];
		
		// 객체 배열 초기화 (랜덤 값으로) 
		for(int i = 0; i < 10; i++) {
			arr[i] = new MyInteger((int)(Math.random() * 100));
		}
	}
 
	
	static Comparator<MyInteger> comp = new Comparator<MyInteger>() {
		
		@Override
		public int compare(MyInteger o1, MyInteger o2) {
			return o1.value - o2.value;
		}
	};
}
 
 
class MyInteger {
	int value;
	
	public MyInteger(int value) {
		this.value = value;
	}
	
}
```

(static을 사용하지 않고도 사용 가능하다. 참고하자.)

우리가 배열을 정려하고 싶을 때 어떤 식으로 저 익명 객체에 있는 기준을 적용시킬까?

Array.sort() 메소드에서는 배열을 받는 것 뿐 아니라 Comparator 또한 파라미터로 받아 그 기준을 적용시킨다.

그 코드는 다음과 같다.

```java
import java.util.Arrays;
import java.util.Comparator;
 
public class Test {
	
	public static void main(String[] args) {
		
		MyInteger[] arr = new MyInteger[10];
		
		// 객체 배열 초기화 (랜덤 값으로) 
		for(int i = 0; i < 10; i++) {
			arr[i] = new MyInteger((int)(Math.random() * 100));
		}
 
		// 정렬 이전
		System.out.print("정렬 전 : ");
		for(int i = 0; i < 10; i++) {
			System.out.print(arr[i].value + " ");
		}
		System.out.println();
		
		Arrays.sort(arr, comp);		// MyInteger에 대한 Comparator을 구현한 익명객체를 넘겨줌
        
		// 정렬 이후
		System.out.print("정렬 후 : ");
		for(int i = 0; i < 10; i++) {
			System.out.print(arr[i].value + " ");
		}
		System.out.println();
	}
 
	
	static Comparator<MyInteger> comp = new Comparator<MyInteger>() {
		
		@Override
		public int compare(MyInteger o1, MyInteger o2) {
			return o1.value - o2.value;
		}
	};
}
 
 
class MyInteger {
	int value;
	
	public MyInteger(int value) {
		this.value = value;
	}
		
}
```



### 내림차순으로 정렬하는 방법

자바의 기본 정렬 기준은 오름차순으로 정해져있다고 했다.

그렇다면 내림차순으로 정렬하고 싶을 때는 어떤 방법을 사용해야할까?

위 Comparable 과 Comparator의 기준을 보면 리턴 값이 음수, 0 ,양수를 기준으로 잡는다는 것을 알 수 있다.

그렇다면 간단하다. 

리턴 시 값을 계산하는 부분을 반대로 하면 된다.

```java
/*
 [오름차순]
 작은 원소가 큰 원소보다 앞에 있으므로 오름차순이다.
 */
public int compareTo(MyClass o) {
	return this.value - o.value;
}
public int compare(Myclass o1, MyClass o2) {
	return o1.value - o2.value;	
}
 
 
/*
 [내림차순]
 큰 원소가 작은 원소보다 앞에 있으므로 내림차순이다.
 */
public int compareTo(MyClass o) {
	return o.value - this.value;
}
public int compare(Myclass o1, MyClass o2) {
	return o2.value - o1.value;
}
```

보면 알겠지만, Comparator는 익명객체로 여러개를 생성할 수 있지만, Comparable의 경우 compareTo 하나 밖에 구현할 수 없다.

그렇다보니, 보통은 Comparable은 여러분이 비교하고자 하는 가장 기본적인 설정(보통은 오름차순)으로 구현하는 경우가 많고, Comparator는 여러개를 생성할 수 있다보니 특별한 정렬을 원할 때 많이 쓰인다. 

쉽게 말해 Comparable은 기본(default) 순서를 정의하는데 사용되며, Comparator은 특별한(specific) 기준의 순서를 정의할 때 사용된다는 것이다.







참고) [자바 [JAVA] - Comparable 과 Comparator의 이해](https://st-lab.tistory.com/243)