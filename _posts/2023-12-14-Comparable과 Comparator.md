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