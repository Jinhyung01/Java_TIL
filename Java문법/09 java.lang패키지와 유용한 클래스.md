## Chapter 09 java.lang패키지와 유용한 클래스

### 1. java.lang패키지

자바프로그래밍에 가장 기본이 되는 클래스들을 포함하고 있다.(import문 없이 사용 가능하는 뜻)

---

### 1.1 Object클래스

- 모든 클래스의 최고 조상이다.
- 오직 11개의 메서드만 가지고 있다.

| Object클래스의 메서드                                        | 설 명                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| protected Object clone()                                     | 객체 자신의 복사본을 반환한다.                               |
| public boolean equals(Object obj)                            | 객체 자신과 객체 obj가 같은 객체인지 알려준다(같으면 true)   |
| protected void finalize()                                    | 객체가 소멸될 때 가비지 컬렉터에 의해 자동적으로 호출된다. 이 때 수행되어야하는 코드가 있을 때 오버라이딩한다.(거의 사용x) |
| public Class getclass()                                      | 객체 자신의 클래스를 담고 있는 Class인스턴스를 반환한다      |
| public int hashcode()                                        | 객체 자신의 해시코드를 반환한다.                             |
| public String toString                                       | 객체 자신의 정보를 문자열로 반환한다.                        |
| public void notify                                           | 객체 자신을 사용하려고 기다리는 쓰레드를 하나만 깨운다.      |
| public void notifyAll()                                      | 객체 자신을 사용하려고 기다리는 모든 쓰레드를 깨운다.        |
| public void wait()<br />public void wait(long timeout)<br />public void wait(longtimeout, int nanos) | 다른 쓰레드가 notify()나 notifyAll()을 호출할 때까지 현제 쓰레드를 무한히 또는 지정된 시간(timeout,nanos)동안 기다리게 한다.(timeout은 1/1000 초, nanos는 1/10^9 초) |

**equals(Object obj)**

- 매개변수로 객체의 참조변수를 받아서 비교하여 그 결과를 boolean값으로 알려준다.

  ```java
  public boolean equals(Object obj){
  	return(this==obj);
  }
  ```

  this는 자기자신의 주소, obj는 매개변수로 주어진 객체의 주소이다. 이 메서드는 두 객체의 주소값을 비교해서 그 결과를 반환하니 항상 false가 나올것이다.(모든 객체의 주소는 다르니까)

  > 객체를 생성할 때, 메모리의 비어있는 공간을 찾아 생성하므로 서로 다른 두 개의 객체가 같은 주소를 갖는 일은 없다.

```java
class EqualsEx1 {
    public static void main(String[] args) {
        Value v1 = new Value(10);
        Value v2 = new Value(10);

        if(v1.equals(v2))
            System.out.println("v1가 v2는 같습니다.");
        else
            System.out.println("v1가 v2는 다릅니다.");

        v2 = v1;
        if(v1.equals(v2))
            System.out.println("v1가 v2는 같습니다.");
        else
            System.out.println("v1가 v2는 다릅니다.");
    }
}
class Value{
    int value;
    Value(int value){
        this.value= value;
    }
}
/* 실행 결과
v1가 v2는 다릅니다.
v1가 v2는 같습니다.
*/
```

위 예제를 통해 Object클래스로부터 상속받은 equals메서드는 두 참조변수에 저장된 값(주소값)이 같은지를 판단하는 기능밖에 할 수 없다는 것을 알 수 있다.

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/56c97c12-b737-4223-9700-998653292242" width="600px" height="200">

​	Value인스턴스가 가지고 있는 value값을 비교하도록 하기 위해서 오버라이딩하여 주소가 아닌 객체에 저장된 내용을 비교하도록 변경하면 된다.

```java
public boolean equals(Object obj){
        if(obj !=null && obj instanceof Person){
            return id == ((Person)obj).id;  
            // obj는 Object타입이므로 id값을 참조하기 위해서 Person타입으로 형변환 해야함
        }
        else{   // Person타입이 아니면 비교할 필요x
            return false;
        }
    }
```

> String,Date,File,wrapper클래스(Integer,Double등)의 equals메서드도 주소값이 아닌 내용을 비교하도록 오버라이딩 되어있다.

**hashCode()**

- 이 메서드는 해싱기법에 사용되는 해쉬함수를 구현한것이다.
- 해싱은 데이터관리기법 중의 하나로 다량의 데이터를 저장하고 검색하는데 유용하다.
- 찾고자하는 값을입력하면, 그 값이 저장된 위치를알려주는 해쉬코드(hashcode)를 반환한다.
- hashCode메서드는 <u>객체의 주소값으로 해시코드를 만들어 반환</u>하기 때문에 32bit JVM에서는 서로 다른  두 객체는 같은 해시코드를 가질 수 없다.(64bit JVm에서는 8byte주소값으로 해시코드(4byte)를 만들기 때문에 중복될 수 있다.  
- equals를 오버라이딩한다면 hashCode메서드도 오버라이딩해야 한다. 
  - 같은 객체라면 해시코드도 같아야 되서

```java
class HashCodeEx1 {
    public static void main(String[] args) {
        String str1 = new String("abc");
        String str2 = new String("abc");

        System.out.println(str1.equals(str2));
        System.out.println(str1.hashCode());
        System.out.println(str2.hashCode());
        // Sytstem.identityHashCode(Object x)는
        // Object클래스의 hashCode처럼 객체의 주소값으로 해시코드를 생성하기에 모든 객체는 항상 다른 해시코드값을 가짐
        // 따라서 str1과 str2가 해시코드는 같지만 서로 다른 객체라는 것을 알 수 있다.
        System.out.println(System.identityHashCode(str1));
        System.out.println(System.identityHashCode(str2));
    }
}
/* 실행 결과
true
96354
96354
1915910607
284720968
*/
```

**toString()**

- 인스턴스에 대한 정보를 문자열로 제공한다

- Object클래스에 정의된 toString()

  ```java
  // 클래스이름 + 16진수의 해시코드를 얻게 된다.
  public String toString(){
  	return getClass().getName() +"@"+Integer.toHexString(hashCode());
  }
  ```

- String클래스 toString()은 String인스턴스가 갖고 있는 문자열을 반환하도록 오버라이딩되어 있다.
- Date클래스의 경우, Date인스턴스가 갖고 있는 날짜와 시간을 문자열로 변환하여 반환하도록 오버라이딩되어 있다.

```java
class Card{
    String kind;
    int number;

    Card(){
        this("SPADE",1);
    }
    Card(String kind, int number){
        this.kind = kind;
        this.number = number;
    }
}
class CardToString{
    public static void main(String[] args) {
        Card c1 = new Card();
        Card c2 = new Card();
        
        // Object클래스의 toString()이 호출
        System.out.println(c1.toString());
        System.out.println(c2.toString());
    }
}
/* 결과
Card@10f87f48
Card@b4c966a
*/
```

위 코드는 toString을 오버라이딩 하지 않아 클래스이름 + 16진수의 해시코드를 얻게 되었다.

다음과 같이 오버라이딩 하면 원하는 정보를 얻을 수 있다.

```java
class Card2{
    String kind;
    int number;

    Card2(){
        this("SPADE",1);
    }
    Card2(String kind, int number){
        this.kind = kind;
        this.number = number;
    }
    // Object클래스에서 toString()의 접근 제어자가 public 이므로 오버라이딩하는 Card2클래스에서도 public
    public String toString(){
        return "kind : "+ kind +", number : "+number;
    }
}
class CardToString2{
    public static void main(String[] args) {
        Card2 c1 = new Card2();
        Card2 c2 = new Card2("Heart",10);

        System.out.println(c1.toString());
        System.out.println(c2.toString());
    }
}
/* 실행결과
kind : SPADE, number : 1
kind : Heart, number : 10
*/
```

**clone()**

- 자신을 복제하여 새로운 인스턴스를 생성
- Object클래스에 정의된 clone()은 단순히 인스턴스 변수의 값만 복사하기 때문에 참타입의 인스턴스 변수가 있는 클래스는 완전한 인스턴스 복제가 이루어지지 않는다.
  - 배열에서 복제된 인스턴스도 같은 배열의 주소를 갖기 때문에 복제된 인스턴스의 작업이 원래의 인스턴스에 영향을 미치게 된다. 따라서 clone()메서드를 오버라이딩해서 새로운 배열을 생성하고 배열의 내용을 복사하도록 해야한다.

```java

class Point implements Cloneable{	// 1. Cloneable인터페이스를 구현
    int x,y;
    Point(int x, int y){
        this.x = x;
        this.y = y;
    }
    
    public String toString() {
        return "x="+x+", y="+y;
    }
    public Object clone(){		// 2. 접근제어자를 public으로 변경
        Object obj =null;		
        try{
            obj=super.clone();  	// 3. try-catch내에서 조상클래스의 clone()을 호출
        }catch(CloneNotSupportedException e){}  // Cloneable인터페이스를 구현하지 않으면 예외발생 이라는 뜻
        return obj;
    }
}

class CloneEx1 {
    public static void main(String[] args) {
        Point original = new Point(3,5);
        Point copy = (Point)original.clone();   // 복제(clone)해서 새로운 객체를 생성
        System.out.println(original);
        System.out.println(copy);

    }
}

```

clone()을 사용하려면 복제할 클래스가 Cloneable인터페이스를 구현해야하고, clone()을 오버라아딩하면서 접근 제어자를 `protected`에서 `public`으로 변경해야 상속관계가 없는 다른 클래스에서 clone()을 호출 할 수 있다.

인스턴스의 <u>데이터를 보호하기 위해서</u> Cloneable인터페이스를 구현한 클래스의 인스턴스만 clone()을 통한 복제가 가능하다. Cloneable<u>인터페이스가 구현되어 있다는 것은 클래스 작성자가 복제를 허용한다는 의미이다</u>.

Object클래스 clone()

```java
public class Object{
	...
    /* native는 자바가 아닌 언어(보통 C나 C++)로 구현한 후 자바에서 사용하려고 할 때 이용하는 키워드이다. 
       자바로 구현하기 까다로운 것을 다른 언어로 구현해서, 자바에서 사용하기 위한 방법이다.
    */
	protected native Object clone() throws CloneNotSupprotedException;
}
```

**공변 반환타입**

- 오버라이딩할 때 <u>조상 메서드의 반환타입을 자손 클래스의 타입으로 변경</u>을 허용하는 것이다.(JDK1.5부터 가능)

- 공변 반환타입을 사용하면 조상의 타입이 아닌, 실제로 반환되는 자손 객체의 타입으로 변환할 수 있어서 <u>번거러운 형변환이 줄어든다</u>는 장점이 있다.

  공변 반환타입 사용 전 코드

  ```java
  public Object clone(){		
          Object obj =null;		
          try{
              obj=super.clone();  	
          }catch(CloneNotSupportedException e){}  
          return obj;
      }
  ```

  ```java
  Point copy = (Point)original.clone();
  ```


  공변 반환타입 사용 후 코드

  ```java
  public Point clone(){ 		// 선언부 반환타입을 Object에서 Point로 변경
  	Object obj = null;
  	try{
  		obj = super.clone();
  	}catch(CloneNotSupportedException e){}
  	return (Point)obj;		// 2. Point타입으로 형변환 한다.
  }
  ```

  ```java
  Point copy = original.clone();
  ```

**배열의 경우**

```java
import java.util.*;
class CloneEx2 {
    public static void main(String[] args) {
        int [] arr = {1,2,3,4,5};
        int [] arrClone = arr.clone();  // 배열 arr을 복제
        arrClone[0]=6;

        System.out.println(Arrays.toString(arr));
        System.out.println(Arrays.toString(arrClone));
    }
}
/* 결과
[1, 2, 3, 4, 5]
[6, 2, 3, 4, 5]
*/	
```

배열도 객체이기 때문에 Object클래스를 상속받으며, Cloneable인터페이스와 Serializable인터페이스가 구현되어 있다. 또 배열에서는  public으로 오버라이딩하여서 직접 호출이 가능하고, 원본과 같은 타입으로 반환하므로(공변 반환타입) 형변환이 필요 없다.

```java
// 두개의 코드는 같은 결과를 가짐

// 코드 1
  int [] arr = {1,2,3,4,5};
  int [] arrClone = arr.clone();
        
// 코드 2
    int [] arr = {1,2,3,4,5};
    int [] arrClone = new int[arr.length];
    System.arraycopy(arr,0,arrClone,0,arr.length);
```

> clone()으로 복제가 가능한 클래스인지 확인하려면 Java API에서 Cloneable을 구현했는지 확인하면 된다!!

**얕은 복사, 깊은 복사**

얕은 복사(shallow copy) : 객체에 저장된 값을 그대로 복제, 객체가 참조하고 있는 객체까지 복제하지 않는다.  기본형인 경우는 문제x, 객체를 복제하는 경우 원본을 변경하면 복사본도 영향을 받는다.

```java
class Circle implements Cloneable{
	Point p;	// 원점(참조변수)
	double r;	// 반지름
	
	Circle(Point p, double r){
		this.p = p;
		this.r = r;
	}
	public Circle clone(){ 		
		Object obj = null;
		try{
			obj = super.clone();
		}catch(CloneNotSupportedException e){}
		
		return (Circle)obj;	
	}
}
class shallowCopy{
	public static void main(String [] args){
		Circle c1 = new Circle(new Point(1,1),2.0);
		Circle c2 = c1.clone();
	}
}
```

결과 그림

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/9c88b8d9-8dfd-4895-b8f2-5a13ace64d01" width="500px" height="200">

깊은 복사(deep copy) : 원본과 복사본이 서로 다른 객체를 참조하기 때문에 원본의 변경이 복사본에 영향을 미치지 않는다.

```java
package ShallowDeep;

class Circle implements Cloneable{
    Point p;
    double r;

    Circle(Point p, double r){
        this.p = p;
        this.r = r;
    }
    public Circle shallowCopy(){    // 얕은 복사
        Object obj=null;
        try{
            obj = super.clone();
        }catch(CloneNotSupportedException e){}
        return (Circle)obj;
    }
    public Circle DeepCopy(){
        Object obj =null;
        try{
            obj=super.clone();
        }catch(CloneNotSupportedException e){}

        Circle c = (Circle)obj;
        c.p = new Point(this.p.x,this.p.y);
        return c;
    }

    public String toString() {
        return "[p="+p+", r="+r+"]";
    }
}
class Point{
    int x, y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
    public String toString(){
        return "("+x+", "+y+")";
    }
}

public class ShallowDeepCopy{
    public static void main(String[] args) {
        Circle c1 = new Circle(new Point(1,1),2.0);
        Circle c2 = c1.shallowCopy();
        Circle c3 = c1.DeepCopy();

        System.out.println("c1="+c1);
        System.out.println("c2="+c2);
        System.out.println("c3="+c3);

        c1.p.x = 9;
        System.out.println("= c1의 변경 후 = ");
        System.out.println("c1="+c1);
        System.out.println("c2="+c2);
        System.out.println("c3="+c3);
    }
}
/* 결과
c1=[p=(1, 1), r=2.0]
c2=[p=(1, 1), r=2.0]
c3=[p=(1, 1), r=2.0]
= c1의 변경 후 = 
c1=[p=(9, 1), r=2.0]
c2=[p=(9, 1), r=2.0]
c3=[p=(1, 1), r=2.0]
*/	
```

이 3줄 코드에 의해 원본이 참조하고 있는 객체까지 복사했다

```java
Circle c = (Circle)obj;
c.p = new Point(this.p.x,this.p.y);
return c;
```

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/2e011c63-21f4-4c6b-a711-d50f4445dedf" width="500px" height="200">

**getClass()**

- 자신이 속한 클래스의 Class객체를 반환하는 메서드

  ```java
  public final class Class implements ...{	// Class 클래스
  		...
  }
  ```

- Class객체는 클래스의 모든 정보를 담고 있으며, 클래스 당 1개만 존재

- 클래스 파일이 클래스 로더(ClassLoader)에 의해서 메모리에 올라갈 때, 자동으로 생성된다.

- 클래스 로더는 실행 시에 필요한 클래스를 동적으로 메모리에 로드하는 역할을 한다.

  - 클래스로더는 파일 형태로 저장되어 있는 클래스를 읽어서 Class클래스에 정의된 형식을 변환함

    (먼저 기존에 생성된 클래스 객체가 메모리에 존재하는지 확인하고, 있으면 객체의 참조를 반환, 없으면 클래스 패스(classpath)에 지정된 경로를 따라서 클래스 파일을 찾는다. 만약 못찾으면 ClassNotFoundException이 발생하고, 찾으면 해당 클래스 파일을 읽어서 Class객체로 반환)

    - 즉 클래스 파일을 읽어서 사용하기 편한 형태로 저장해 놓은 것이 클래스 객체이다.

   <img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/f132274c-447f-4d8b-96f5-5e10869c148e" width="600px" height="200">

**Class객체를 얻는 방법**

해당 Class객체에 대한 참조를 얻는 여러가지 방법

```java
Class cObj = new Card().getclass();	// 생성된 객체로 부터 얻는 방법
Class cObj = Card.class;			// 클래스 리터럴(*.class)로 부터 얻는방법
Class cObj = Class.forName("Card");	// 클래스 이름으로 부터 얻는 방법
// forName()은 데이터베이스 드라이버를 메모리에 올릴때 주로 사용
```

Class객체를 이용하면 클래스에 대한 모든 정보를 얻을 수 있기에 Class 객체를 통해서 객체를 생성하고 메서드를 호출하는 등 동적인 코드를 작성할 수 있다.

```java
Card c = new Card();				// new연산자를 이용해서 객체 생성
Card c = Card.class.newInstance();	// Class객체를 이용해서 객체 생성
```

> 동적으로 객체를 생성하고 메서드를 호출하는 방법을 알고 싶다면, 'reflection API'로 검색하면된다.

```java
package ClassEx;

final class Card {
    String kind;
    int num;
    Card(){
        this("SPADE",1);
    }
    Card(String kind, int num){
        this.kind = kind;
        this.num = num;
    }
    public String toString(){
        return kind + ":"+num;
    }
}
class ClassEx1{
    public static void main(String[] args) throws Exception{
        Card c = new Card("HEART",3);   // new연산자로 객체 생성
        Card c2 = Card.class.newInstance();       // Class객체를 통해서 객체 생성

        Class cObj = c.getClass();

        System.out.println(c);
        System.out.println(c2);
        System.out.println(cObj.getName());
        System.out.println(cObj.toGenericString());
        System.out.println(cObj.toString());
    }
}
/* 결과
HEART:3
SPADE:1
ClassEx.Card
final class ClassEx.Card
class ClassEx.Card
*/
```

### 1.2 String 클래스

문자열을 위한 변경 불가능한(immutable)클래스이다.

String 클래스에서 문자열을 저장하기 위해서 문자형 배열 참조변수(char[] value)를 인스턴스 변수로 정의해놓고 있다. 인스턴스 생성시 생성자의 매개변수로 입력받는 문자열은 이 value에 char[]로 저장되는 것이다.

```java
public final class String implements java.io.Serializable, Comparable{
	private char[] value;
}
```

한번 생성된 String인스턴스가 갖고 있는 문자열은 읽어 올 수만 있고, 변경 불가능

`+`연산자를 이용해 문자열을 결합하는 경우

```java
String a = "a";
String b = "b";
	   a = a+b;			
```

인스턴스내의 문자열이 바뀌는 것이 아니라 새로운 문자열("ab")이 담긴 String인스턴스가 생성되는 것이다.

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/77e3c229-6461-4cf3-83b3-26d490ed136a" width="600px" height="200">

> +을 사용해서 문자열을 결합하는 것은 매 연산 시마다 새로운 문자열을 가진 String인스턴스가 생성되어 메모리공간을 차지하게 되므로 가능한 결합횟수를 줄이는 것이 좋다. 문자열간의 결합이나 추출 등 문자열을 다루는 작업이 많이 필요한 경우면 StringBuffer클래스를 사용하는 것이 좋다.

**문자열 만드는법**

```java
String str1 = "abc";	// 문자열 리터럴 "abc"의 주소가 str1에 저장됨
String str2 = "abc";	// 문자열 리터럴 "abc"의 주소가 Str2에 저장됨

String str3 = new String("abc");		// 새로운 String인스턴스를 생성
String str4 = new String("abc");		// 새로운 String인스턴스를 생성
```

- String클래스의 생성자 이용 한경우

  new연산자에 의해서 메모리할당이 이루어지기 때문에 항상 새로운 String인스턴스가 생성된다.

​		<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/af403bd9-3164-4754-a82a-e57f81f3aaaf" width="500px" height="160">

- 문자열 리터럴의 경우

  이미 존재하는 것을 재사용 하는 것이다.

  <img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/89cc74b4-25bb-49b5-a22c-6414a6166221" width="500px" height="160">

eqauls()를 사용했을때는 두 문자열의 내용("abc")를 비교하기 떄문에 모두 true

참고 예

```java
class StringEx1 {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = "abc";
        System.out.println("String str1 = \"abc\";");
        System.out.println("String str2 = \"abc\";");

        System.out.println("str1 == str2 ? " +(str1==str2));
        System.out.println("str1.equals(str2) ? " + str1.equals(str2));
        System.out.println();

        String str3 = new String("\"abc\"");
        String str4 = new String("\"abc\"");
        System.out.println("String str3 = new String(\"abc\");");
        System.out.println("String str4 = new String(\"abc\");");

        System.out.println("str3 == str4 ? "+(str3==str4));
        System.out.println("str3.equals(str4) ? " + str3.equals(str4));
    }
}
/* 결과
String str1 = "abc";
String str2 = "abc";
str1 == str2 ? true
str1.equals(str2) ? true

String str3 = new String("abc");
String str4 = new String("abc");
str3 == str4 ? false
str3.equals(str4) ? true
*/
```

**문자열 리터럴**

- 자바 소스파일에 포함된 모든 문자열 리터럴은 컴파일 시에 클래스 파일에 저장된다

- 같은 내용의 문자열 리터럴은 한번만 저장된다.(문자열 리터럴도 String인스턴스이고, 한번 생성하면 내용을 변경할 수 없으니 하나의 인스턴스를 공유하면 되기 때문에)

- 클래스 파일에는 소스파일에 포함된 모든 리터럴의 목록 이있다. 해당 클래스 파일이 클래스 로더에 의해 메모리에 올라갈 때, 이 리터럴의 목록에 있는 리터럴들이 JVM내에 있는 `상수 저장소(constant pool)`에 저장된다. 이 과정에서  문자열리터럴의 String인스턴스가 생성되는 것이다. 즉 이곳에 AAA와 같은 문자열 리터럴이 자동적으로 생성되어 저장되는 것이다.

  ```java
  class StringEx2{
  	public static void main(String [] args){
  		String s1 = "AAA";
  		String s2 = "AAA";
  		String s3 = "AAA";
  		String s4 = "BBB";
  	}
  }
  ```

  실행하면 AAA를 담고있는 String인스턴스가 하나 생성된 후, 참조변수 s1,s2,s3는 모두 이 String인스턴스를 참조하게 된다.

**빈 문자열(empty string)**

빈 문자열의 내부적 배열

```java
char[] chArr = new char[0];	// 길이가 0인 char배열	
int [] iArr  = {};		// 길이가 0인 int 배열
```

> 참고 
>
> C언어에서는 문자열의 끝네 널 문자가 항상 붙지만, 자바에서는 널 문자를 사용하지 않고 문자열의 길이정보를 따로 저장한다.

String은 참조형 타입의 기본값인 null 보다는 빈 문자열, char형은 기본값인 '\u0000'대신 공백으로 초기화 하는 것이 보통이다.

```java
String s = null
// \u0000은 유니코드의 첫번째 문자로써 아무런 문자도 지정되지 않은 빈 문자이다.
char c = '\u0000'
// 위 코드 대신에
String s ="";	// 빈 문자열로 초기화
char c = ' ';	// 공백으로 초기화
```

```java
class StringEx3 {
    public static void main(String[] args) {
        char[] cArr = new char[0];  // char[] cArr = {};와 같다.
        String s = new String(cArr);    // String s = new String(""); 와같다.

        System.out.println("cArr.length ="+cArr.length);
        System.out.println("@@@"+s+"@@@");
    }
}
/*실행 결과
cArr.length =0
@@@@@@
*/
```

#### String클래스의 생성자와 메서드

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/64fcf81c-a55c-4ad9-a85b-d965e8b0cce1" width="800px" height="800">

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/bceac6b6-1749-4770-a15d-732b08eb289e" width="800px" height="800">

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/6bc236ff-ac4c-4ab5-8fe9-f4a92b212786" width="800px" height="800">

**replace()와 (replaceAll(),replaceFirst) 차이**

```java
public class diff {
    public static void main(String[] args) {
        String a = "Hello Hello";
        // String replace(CharSequence old, CharSequence new)
        System.out.println(a.replace("ll","LL"));
        // String replaceAll(String regex, String replacement)
        System.out.println(a.replaceAll("ll","LL"));
        // String replaceFirst(String regex, String replacement)

        String s = "안녕하세여 hello world 환영해요";
        // repalce
        System.out.println(s.replace("[a-z]",""));
        // replaceAll 은 정규식 적용가능(a-z 소문자 부분 다 공백)
        System.out.println(s.replaceAll("[a-z]",""));
         // replaceFirst 정규식 적용가능
        System.out.println(s.replaceFirst("[a-z]",""));
    }
}
/* 실행 결과
HeLLo HeLLo
HeLLo HeLLo
HeLLo Hello
안녕하세여 hello world 환영해요
안녕하세여   환영해요
안녕하세여 ello world 환영해요
*/
```



#### join()과 java.util.StringJoiner(JDK 1.8 부터 추가)

join

- 여러 문자열 사이에 구분자를 넣어서 결합한다.(split()과 반대 작업)

```java
String animals = "dog,cat,bear";
String[] arr = animals.split(",");	// 문자열을 ','를 구분자로 나눠서 배열에 저장
// String.join("-", "dog","cat","bear");
String str = String.join("-",arr);	// 배열의 문자열을 '-'로 구분해서 결합

System.out.println(str);		// dog-cat-bear
```

java.util.StringJoiner클래스

```java
							//    구분자 , 처음에오는 문자,끝에오는 문자
StringJoiner sj = new StringJoiner(","   ,   "["   ,   "]");
String[] strArr = {"aaa","bbb","ccc"};

for(String s : strArr)
    sj.add(s.toUpperCase());

System.out.println(sj.toString());	// [AAA, BBB, CCC]
```

예제

```java
import java.util.StringJoiner;
class StringEx4 {
    public static void main(String[] args) {
        String animals = "dog,cat,bear";
        String [] arr = animals.split(",");

        System.out.println(String.join("-",arr));
        StringJoiner sj = new StringJoiner("/","[","]");
        for(String s : arr)
            sj.add(s);
        System.out.println(sj.toString());
    }
}
/* 결과
dog-cat-bear
[dog/cat/bear]
*/	
```

**유니코드의 보충문자**

유니코드는 원래 2byte(16bit) 문자체계인데, 이걸로도 모자라서 20bit로 확장하게 되었다.  그래서 확장된 문자를 다루기 위해서는 하나의 문자를 char타입으로 다루지 못하고, int타입으로 다룰 수 밖에 없다.

결론 

- 매개변수의 타입이 `int ch`인 것들은 보충문자를 지원한다.

- 매개변수의 타입이 `char ch`인 것들은 보충문자를 지원하지 않는다.

> 확장에 의해 새로 추가된 문자들을 `보충 문자(supplementary characters)`라고 한다.

**문자 인코딩 변환**

`getBytes(String charsetName)`

- 문자열의 문자 인코딩을 다른 인코딩으로 변경할 수 있다.

  자바가 UTF-16을 사용하지만, 문자열 리터럴에 포함되는 문자들은 os의 인코딩을 사용한다.

  한글 windows의 경우 문자 인코딩으로 CP949를 사용하며, UTF-8로 변경하려면 다음과 같이 하면된다.

  ```java
  byte[] utf8_str = "가".getBytes("UTF-8");		// 문자열을 UTF-8로 변환
  String str = new String(utf8_str, "UTF-8");		 // byte배열을 문자열로 변환
  ```

  > 서로 다른 문자 인코딩을 사용하는 컴퓨터 간에 데이터를 주고받을때는 적절한 문자 인코딩이 필요하다.

  ```java
  import java.io.UnsupportedEncodingException;
  import java.util.StringJoiner;
  
  class StringEx5 {
      public static void main(String[] args) {
          String str = "가";
          byte[] bArr= {};
          byte[] bArr2 = {};
          try {
              bArr = str.getBytes("UTF-8");	// 문자열 "가"를 UTF-8로 변환 bArr에 16진수 0xEAB080이 저장됨
              bArr2 = str.getBytes("CP949");	// 문자열 "가"를 CP949로 변환 bArr에 16진수 0xB0A1이 저장됨
          }
          catch(UnsupportedEncodingException e){
              e.printStackTrace();
          }
          System.out.println("UTF-8:"+ joinByteArr(bArr));
          System.out.println("CP949:"+ joinByteArr(bArr2));
          try {
              // byte배열을 문자열로 변환
              System.out.println("UTF-8:" + new String(bArr, "UTF-8"));
              System.out.println("CP949:" + new String(bArr2, "CP949"));
          }catch(UnsupportedEncodingException e){
              e.printStackTrace();
          }
  
      }
      static String joinByteArr(byte[] bArr){
          StringJoiner sj = new StringJoiner(":","[","]");
  
          for(byte b : bArr)
              sj.add(String.format("%02X",b));
          return sj.toString();
      }
  }
  /*결과
  UTF-8:[EA:B0:80]
  CP949:[B0:A1]
  UTF-8:가
  CP949:가
  */
  ```

**String.format()**

- 형식화된 문자열을 만들어내는 간단한 방법이다. printf()와 사용법이 완전이 똑같음

```java
String str = String.format("%d 더하기 %d는 %d입니다.",3,5,3+5);
System.out.println(str);	// 3 더하기 5는 8입니다.
```

**기본형 값을 String으로 변환**

```java
int i = 100;						// 100을 "100"으로 변환하는 방법
String str1 = i +"";				// 1. 빈 문자열을 더한다.(가독성이 높음)
String str2 = String.valueOf(i);	// 2. valueOf() 사용(성능이 좋지만 가독성이 떨어짐)
```

valueOf종류

```java
String.valueOf(boolean b)
String.valueOf(char c)
String.valueOf(int i)
String.valueOf(long l)
String.valueOf(float f)
String.valueOf(double d)
```

**String을 기본형 값으로 변환**

```java
int i  = Integer.parseInt("100");	// 1. parseInt() 사용
int i2 = Integer.valueOf("100");   // 2. valueOf() 사용
							       // 반환타입이 Integer이지만 오토박싱에 의해 int로 자동변환됨
```

메서드의 이름을 통일하기 위해 valueOf()가 추가되었다.

```java
// valueOf(String s)는 메서드 내부에서 그저 parseInt(String s)를 호출할 뿐이므로, 반환 타입만 다르지 같은 메서드이다.
public static Integer valueOf(String s) throws NumberForamtException{
	return Integer.valueOf(parseInt(s,10));	// 10은 10진수를 의미
}
```

다양한 문자열->기본형 변환 방법

```java
Boolean.parseBoolean(String s)
Byte.parseByte(String s)
Short.parseShort(String s)
Integer.parseInt(String s)
Long.parseLong(String s)
Float.parseFloat(String s)
Double.parseDouble(String s)
```

예제

```java
class StringEx6{
    public static void main(String[] args) {
        int iVal = 100;
        String strVal = String.valueOf(iVal);   // 100을 "100"으로 변환
        
        double dVal = 200.0;
        String strVal2 = dVal + "";  // 빈 문자열을 이용해 "200.0"으로 변환
									// 부호 +라는 의미
        double sum = Integer.parseInt("+"+ strVal)+ Double.parseDouble(strVal2);
        double sum2 = Integer.valueOf(strVal) + Double.valueOf(strVal2);

        System.out.println(String.join("",strVal,"+",strVal2,"=")+sum);
        System.out.println(strVal +"+"+strVal2+"="+sum2);
    }
}
/* 결과
100+200.0=300.0
100+200.0=300.0
*/
```

주의사항

- parseInt()나 parseFloat()같은 메서드는 문자열에 공백 또는 문자가 포함되어 있는 경우 변환 시 예외(NumberFormat Exception)가 발생할 수 있으므로 주의해야함. 그래서 trim()을 습관적으로 같이 사용하기도 함

  ```java
  int val = Integer.parseInt(" 123 ".trim());	// 문자열 양 끝의 공백을 제거후 반환
  ```

- 부호를 의미하는 '+'나 소수점을 의미하는 '.'와 float형 값을 뜻하는 f와 같은 자료형 접미사는 허용된다. 단 자료형에 알맞는 변환을 하는 경우에만 허용( ex) 1.0f를 Float.parseFloat(String s)에 사용해야 문제가 없다)

  > +가 포함된 문자열이 parseInt()로 변환가능하게 된것은 JDK1.7부터

  참고!!

  - Integer클래스의 static int parseInt(String s, int radix)를 사용하면 16진수 값으로 표현된 문자열도 변환할 수 있다. 대소문자 구별 없이 a,b,c,d,e,f도 사용 가능


  ```java
  // Integer.valueOf("a",16);	
  int result = Integer.parseInt("a",16);
  
  // reuslt에 10이 저장됨(16진수 a는 10진수로 10을 뜻한다.);
  
  // 만약 이처럼 숫자가 아닌 것을(a)를 숫자로 변환하려하면 NumberFormatException이 발생한다.
  System.out.println(Integer.parseInt("a"));
  ```


**substring메서드**

`substring(int start, int end)`

```java
public class StringEx7 {
    public static void main(String[] args) {
        String fullName = "Hello.java";
        // fullName에서 '.'의 위치를 찾는다.
        int index = fullName.indexOf(".");

        // fullName의 첫번째 글자부터 '.'이 있는 곳까지 문자열을 추출한다.
        String fileName = fullName.substring(0,index);

        // '.'의 다음 문자부터 시작해서 문자열의 끝까지 추출한다.
        // fullName.substring(index+1,fullName.length()); 의 결과와 같다.
        String ext = fullName.substring(index+1);
        System.out.println(fullName + "의 확장자를 제외한 이름은 "+fileName);
        System.out.println(fullName+"의 확장자는 "+ext);
    }
}
/* 결과
Hello.java의 확장자를 제외한 이름은 Hello
Hello.java의 확장자는 java
*/
```

> end - start = substring에 의해 추출될 글자의 수

### 1.3 StringBuffer클래스와 StringBuilder클래스

**StringBuffer클래스**

- 인스턴스를 생성할 때 지정된 문자열을 변경할 수 있다.(String클래스는 바꾸지 못함)
- 내부적으로 문자열 편집을 위한 버퍼(buffer)를 가지고 있으며, StringBuffer인스턴스를 생성할 때 그 크기를 지정할 수 있다.

```java
// 문자열을 저장하기 위한 char형 배열의 참조변수를 인스턴스변수로 선언해 놓음
// 인스턴스가 생성될 때 char형 배열이 생성되며 이 때 생성된 char형 배열을 인스턴스변수 value가 참조하게 된다.
public final class StringBuffer implements java.io.Serializable{
	private char[] value;
	...
}
```

**StringBuffer의 생성자**

```java
public StringBuffer(int length){
	value = new char[legnth];
	shared = false;
}
// 버퍼의 크기를 지정하지 않으면 버퍼의 크기는 16이 된다.
public StringBuffer(){
	this(16);
}
// 지정한 문자열의 길이보다 16이 더 크게 버퍼를 생성한다.
public StringBuffer(String str){
	this(str.length()+16);
}
```

> 버퍼의 길이를 충분히 잡아주는게 좋다. 버퍼의 크기가 작업하려는 문자열의 길이보다 작을 때는 내부적으로 버퍼의 크기를 증가시키는 작업이 수행되어야하기 때문에 작업효율이 떨어짐

배열의 길이는 변경될 수 없으므로 새로운 길이의 배열을 생성한 후에 이전 배열의 값을 복사해야 한다.

```java
// StringBuffer 클래스의 일부
...
// 새로운 길이(newCapacity)의 배열을 생성한다. newCapacity는 정수값이다.
char newValue[] = new char[newCapacity];

// 배열 value의 내용을 배열 newValue로 복사한다.
System.arraycopy(value,0,newValue,0,count);	// count는 문자열의 길이
value = newValue;  // 새로 생성된 배열의 주소를 참조변수 value에 저장
```

**StringBuffer의 변경**

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("123");	// sb의 내용 뒤에 "123"을 추가한다.
```

<u>append()는 반환타입이 StringBuffer로 자신의 주소를 반환한다.</u> 

그래서 아래의 코드는 sb에 새로운 문자열이 추가되고 sb자신의 주소를 반환하여 sb2에는 sb의 주소인 0x100이 저장된다.

```java
StringBuffer sb2 = sb.append("ZZ");	// sb의 내용뒤에 "ZZ"를 추가한다.
System.out.println(sb);		// abc123ZZ
System.out.println(sb2);	// abc123ZZ
```

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/3648f225-596d-4b13-bc09-eb6395765fe5" width="600px" height="200">

반환하는 주소가 자기 주소이니 같은 StringBuffer인스턴스를 가리키고 있다. 그래서 하나의 StringBuffer인스턴스에 대해 아래와 같이 연속적으로 append()를 호출하는 것이 가능

> 만약 append()의 반환타입이 void이면 불가능 했을 것이다.

```java
StringBuffer sb = new StringBuffer("abc");
// sb.append("123")이 곧 sb이니(반환타입이 StringBuffer,자신의 주소) sb.append("ZZ")와 같은 의미가 된다.
sb.append("123").append("ZZ");
```

**StringBuffer의 비교**

StringBuffer클래스는 `equals메서드`를 오버라이딩하지 않았다. 따라서 equals메서드를 사용해도 등가비교연산자(==)로 비교환 것과 같은 결과를 얻는다.

```java
StringBuffer sb = new StringBuffer("abc");
StringBuffer sb2 = new StringBuffer("abc");

System.out.println(sb==sb2);     	// false
System.out.println(sb.equals(sb2))  // false
```

`toString()`은 StringBuffer인스턴스에 담고있는 문자열을 String으로 반환하도록 오버라이딩 되어 있기 때문에

StringBuffer인스턴스에 toString()을 호출해서 String인스턴스를 얻은 다음, 여기에 equals메서드를 사용해서 비교해야한다.

```java
String s = sb.toString();
String s2 = sb2.toString();

System.out.println(s.equals(s2));	// true
```

**StringBuffer클래스의 생성자와 메서드**

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/d183b15d-0636-4313-a1db-88cbd2b20b5a" width="800px" height="800">

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/354d6d0e-645a-4a41-b8d7-27d256549770" width="800px" height="800">

예제

```java
public class StringBufferEx2 {
    public static void main(String[] args) {
        // "01"의 문자열 길이인 2 + 16 = 18, 즉 버퍼의 크기는 18이된다.
        StringBuffer sb = new StringBuffer("01");
        StringBuffer sb2 = sb.append(23);
        sb.append('4').append(56);

        StringBuffer sb3 = sb.append(78);
        sb3.append(9.0);
        System.out.println(sb.capacity());
        System.out.println("sb =" + sb);
        System.out.println("sb2 =" + sb2);
        System.out.println("sb3 =" + sb3);

        System.out.println("sb =" + sb.deleteCharAt(10));
        System.out.println("sb =" + sb.delete(3, 6));
        System.out.println("sb =" + sb.insert(3, "abc"));
        System.out.println("sb =" + sb.replace(6, sb.length(), "END"));

        System.out.println("capacity=" + sb.capacity());
        System.out.println("length=" + sb.length());
    }
}
/* 결과
18
sb =0123456789.0
sb2 =0123456789.0
sb3 =0123456789.0
sb =01234567890
sb =01267890
sb =012abc67890
sb =012abcEND
capacity=18
length=9
*/
```

**StringBuilder**

StringBuffer는 멀티쓰레드에 안전(thread safe)하도록 동기화 되어있다. 동기화가 StringBuffer의 성능을 떨어뜨림. 멀티쓰레드로 작성된 프로그램이 아닌 경우, StringBuffer의 동기화는 불필요하게 성능만 떨어뜨린다. 그래서 StringBuffer에서 쓰레드의 동기화만 뺀 `StringBuilder`가 새로 추가 됨

StringBuilder는 StringBuffer와 완전히 똑같은 기능으로 작성되어 있다. 그래서 StringBuffer타입의 참조변수를 선언한 부분과 StringBuffer의 생성자만 바꾸면 된다.

```java
// StringBuffer
StringBuffer sb;
sb = new StringBuffer();
sb.append("abc");

// StringBuilder
StringBuilder sb;
sb = new StringBuilder();
sb.append("abc");
```

> StringBuffer도 충분히 성능이 좋기 때문에 성능향상이 반드시 필요한 경우를 제외하고는 StringBuilder로 굳이 바꿀 필요 없다.

### 1.4 Math클래스

Math클래스의 메서드는 모두 static이며, 2개의 상수만 정의해 놓았다.

> 클래스 내에 인스턴스변수가 하나도 없으므로 인스턴스를 생성할 필요가 없다. 따라서 생성자의 접근 제어자를 private로 지정해 다른 클래스에서 Math인스턴스를 생성할 수 없도록 함

```java
public static final double E  = 2.7182818284590452354;	// 자연로그의 밑
public static final double PI = 3.14159265358979323846; // 원주율 
```

**올림, 버림, 반올림**

`Math.round()` : 소수점 첫째자리에서 반올림을 해 정수값(long)을 반환

n+1째자리에서 반올림된 소수점 n자리 값을 얻으려면 
$$ {dasdsaas}
10^n을 곱하고 10^n.0으로 나눠주면된다.
$$
만약 90.7552를 소수점 셋째자리에서 반올림한 후 소수점  두 자리까지의 값만을 얻고 싶은 경우 다음과 같이 하면 된다.

```
1. 원래 값에 100을 곱한다.
					90.7552 * 100 -> 9075.52
2. 위의 결과에 Math.round()를 사용한다.
					Math.round(9075.52) -> 9076
3. 위의 결과를 다시 100.0으로 나눈다.
					9076 /100.0 -> 90.76 	
					// 만약 100을 나누면 90이 되어 원하는 값을 얻지 못하게 된다.
```

**Math.round, Math.ceil, Math.floor, Math.rint비교**

```java
// Math.ceil()  올림
System.out.println(Math.ceil(1.1));     // 2.0 
System.out.println(Math.ceil(-1.5));    // -1.0
 
// Math.floor() 버림
System.out.println(Math.floor(1.1));    // 1.0 
System.out.println(Math.floor(-1.5));   // -2.0

// Math.round() 반올림, 반환값이 int
// round()는 소수점 첫째자리가 5일 때, 더 큰값으로 반올림한다.
 System.out.println(Math.round(-1.5));	// -1
 System.out.println(Math.round(-1.2));	// -1
 System.out.println(Math.round(-1.7));	// -2

// Math.rint() 반올림, 반환값이 double
// rint()는 소수점 첫째자리가 5일 때, 짝수를 반환한다.
System.out.println(Math.rint(-1.5));	// -2.0
System.out.println(Math.rint(-1.2));	// -1.0
System.out.println(Math.rint(-1.7));	// -2.0
```

**예외를 발생시키는 Math클래스의 메서드**

정수형간의 연산에서 발생할 수 있는 정수형 overflow를 감지하기 위해 메서드 이름에 `Exact` 가 포함된 메서드들이 JDK1.8부터 추가됨

```java
// 연산자는 결과를 반환할뿐 오버플로우의 발생여부에 대해 알려주지 않지만
// 아래의 메서드들은 오버플로우가 발생하면, 예외(ArithmeticException)을 발생시킨다.

int addExact(int x, int y)			// x + y
int subtractExact(int x, int y)		// x - y
int multiplyExact(int x, int y)		// x * y
int incrementExact(int a)			// a++
int decrementExact(int a)			// a--
int negateExact(int a)				// -a (~a +1) ~a의 결과가 int최대값일때 1을 더하면 오버플로우 
int toIntExact(long value)			// (int)value
```

negateExact(int a) 예제

```java
import static java.lang.System.*;
import static java.lang.Math.*;

class MathEx2 {
    public static void main(String[] args) {
        int i = Integer.MIN_VALUE;

        out.println("i = "+i);	// i = -2147483648
        
        /* overflow가 발생해도 예외를 발생시키지 않고 연산 결과만 반환
      	  -i = ~i + 1
          ~i = 2147483648(int의 최댓값)
          ~i + 1 = overflow에 의해 -2147483648
        */
        out.println("-i= "+(-i));

        try{
            out.printf("negateExact(%d)= %d\n",10,negateExact(10));
            out.printf("negateExact(%d)= %d\n",-10,negateExact(-10));
            out.printf("negateExact(%d)= %d\n",i,negateExact(i));      // 예외 발생
        }catch (ArithmeticException e){
            out.printf("negateExact(%d)= %d\n",(long)i,negateExact((long)i));
        }
    }
}
/* 실행결과

-i= -2147483648
negateExact(10)= -10
negateExact(-10)= 10
negateExact(-2147483648)= 2147483648
*/
```

**삼각함수와 지수, 로그**

```java
Math.sqrt(double a) // a의 제곱근 계산(루트 a)
Math.pow(double a,double b) // a의 b제곱
Math.sin(double a)  // sin( a(rad) )
Math.cos(double a)	// cos( a(rad) )
Math.toRadians(double angdeg)	// 도를 라디안 단위로 변환
Math.toDegrees(double angrad)	// 라디안을 도로 변환
Math.atan2(double y, double x)	// 주어진 좌표 (x, y)에서의 각도를 계산할 때 x축과의 각도를 라디안 단위로 반환
Math.log10(double a)	// log10(a)
```

예제

```java
public class MathEx3 {
    public static void main(String[] args) {
        int x1 = 1, y1 = 1; // (1,1)
        int x2 = 2, y2 = 2; // (2,2)

        // (1,1)과 (2,2) 두 점 사이의 길이
        double c = Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
        double a = c * Math.sin(Math.PI / 4);	
        double b = c * Math.cos(Math.PI / 4);
        // double b = c * Math.cos(Math.toRadians(45));

        System.out.printf("a=%f\n",a);	// a=1.000000
        System.out.printf("b=%f\n",b);	// b=1.000000
        System.out.printf("c=%f\n",c);	// c=1.414214
        // a와 b사이의 각도 rad
        System.out.printf("angle=%f rad\n",Math.atan2(a,b));	// angle=0.785398 rad
        System.out.printf("angle=%f degree\n\n",Math.atan2(a,b)*180/Math.PI);	// angle=45.000000 degree
    //  System.out.printf("angle=%f degree\n\n",Math.toDegrees(Math.atan2(a,b)));
        
        // 2^24 = 10^x 구하는 문제(24자리의 2진수는 10진수로 몇자리의 값인지 알아내기)
        // 양변에 상용로그 취하면 24*log10(2) = x
        // 결과가 약7.2 -> 10진수로 7자리의 값을 표현할 수 있다는 뜻 즉, float타입의 정밀도가 7자리인 것이다.
        System.out.printf("24 * log10(2)=%f\n",24 * Math.log10(2));	// 7.224720 
        
        
        // 2^53 = 10^x 구하는 문제(53자리의 2진수는 10진수로 몇자리의 값인지 알아내기)
        // 양변에 상용로그 취하면 53*log10(2) = x
        // 결과가 약15.9 -> 10진수로 15자리의 값을 표현할 수 있다는 뜻 즉, double타입의 정밀도가 15자리인 것이다.
        System.out.printf("53 * log10(2)=%f\n",53 * Math.log10(2));	// 15.954590
    }
}
```

> float타입의 가수는 23자리지만, 정규화를 통해 1자리를 더 확보할 수 있으므로 실제로 저장할 수 있는 가수는 24자리이다. 
>
> double타입도 역시 52+1 = 53자리이다.

**StrictMath클래스**

Math클래스는 최대한의 성능을 얻기 위해 JVM이 설치된 OS의 메서드를 호출해서 사용한다.(**OS에 의존적인 계산**)

예를 들어 부동소수점 계산의 경우 반올림의 처리방법 설정이 OS마다 다를 수 있기 때문에 자바로 작성된 프로그램임에도 불구하고 컴퓨터마다 결과가 다를 수 있다. 

```
이러한 차이를 없애기 위해 성능은 다소 포기하고 어떤 OS에서 실행되어도 항상 같은 결과를 얻도록
Math클래스를 새로 작성한 것이 StrictMath클래스
```

**Math클래스의 메서드**

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/0ce0b6e9-b915-44ec-be9a-bd7fc6e17d25" width="800px" height="600">

<img src="https://github.com/Jinhyung01/Java_TIL/assets/129172593/22248ffb-0223-4592-a5c6-296c585c6e43" width="800px" height="600">

### 1.5 래퍼(wrapper)클래스

객체지향 개념 : 모든 것은 객체로 다루어져야 한다.  하지만 높은 성능을 얻기위해 8개의 기본형을 객체로 다루지 않는다.(자바가 완전한 객체지향 언어가 아니라는 얘기를 듣는 이유)

기본형 변수도 객체로 다뤄야 하는 경우가 있다.

- 매개변수로 객체를 요구할때
- 기본형 값이 아닌 객체로 저장해야할 때
- 객체간의 비교가 필요할때

이럴 때 사용되는 것이 `wrapper클래스`이다. 

```java
// int형의 래퍼 클래스인 Integer클래스의 실제코드
public final class Integer extends Number implements Comparable{
		...
	private int value;
	...
}
// 각 자료형에 알맞은 값을 내부적으로 저장하고 있다.
```

래퍼 클래스의 생성자

| 기본형  | wrapper클래스 | 생성자                                                       | 활용 예                                                      |
| ------- | ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| boolean | Boolean       | Boolean (boolean value)<br />Boolean (String s)              | Booelan b = new Boolean (true);<br />Boolean b = new Booelan ("true"); |
| char    | Character     | Character (char value)                                       | Character c = new Character ('a');                           |
| byte    | Byte          | Byte (byte value)<br />Byte (String s)                       | Byte b = new Byte(10);<br />Byte b = new Byte("10");         |
| short   | Short         | Short (short value)<br />Short (String s)                    | Short s = new Short(10);<br />Short s = new Short("10");     |
| int     | Integer       | Integer (int value)<br />Integer (String s)                  | Integer i = new Integer(100);<br />Integer i = new Integer("100"); |
| long    | Long          | Long (long value)<br />Long (String s)                       | Long l = new Long(100);<br />Long l = new Long("100");       |
| float   | Float         | Float (double value)<br />Float (float value)<br />Float (String s) | Float f = new Float(1.0);<br />Float f = new Float(1.0f);<br />Float f = new Float("1.0"); |
| double  | Double        | Double (double value)<br />Double (String s)                 | Double d = new Double(1.0);<br />Double d = new Double("1.0"); |

> 주의할점 !!  생성자의 매개변수로 문자열을 제공할 때, 각 자료형에 알맞은 문자열을 사용해야한다.
>
> > 예시 : new Integer("1.0"); -> NumberFormatException발생

예제

```java
class WrapperEx1 {
    public static void main(String[] args) {
        // Java 9부터는 Integer(int)와 같은 생성자들이 @Deprecated로 표시되어 있다.
        // 이는 해당 생성자가 사용을 권장하지 않는다는 것을 의미
        Integer i = new Integer(100);
        Integer i2 = new Integer(100);

        System.out.println("i==i2 ? "+(i==i2));		// false
        System.out.println("i.equals(i2) ? "+i.equals(i2));		// true
        
        // i가 i2보다 크다면 양수, 작다면 음수, 같다면 0
        System.out.println("i.compareTo(i2)="+i.compareTo(i2));	// 0
        System.out.println("i.toString()="+i.toString());		// 100

        System.out.println("MAX_VALUE="+Integer.MAX_VALUE);		// 2147483647
        System.out.println("MIN_VALUE="+Integer.MIN_VALUE);		// -2147483648
        System.out.println("SIZE="+Integer.SIZE+"bits");		// 32bits
        System.out.println("BYTES="+Integer.BYTES+"bytes");		// 4bytes
        System.out.println("TYPE="+Integer.TYPE);				// int
    }
}
/* 만약 Integer i = 100; Integer i2 = 100;처럼 오토박싱이 된 경우에는 == 비교연산자를 사용할 수 없다.
   값이 같으면 서로다른 객체인가를 비교하는 ==가 true로 나와서 따라서 compareTo()를 제공
*/
```

**Number클래스**
추상클래스로 내부적으로 숫자를 멤버변수로 갖는 wrapper 클래스들의 조상이다.

![](C:\Users\이진형\OneDrive\바탕 화면\Java TIL\Java문법\img\09_Number클래스.png)

다음 클래스는 연산자의 역할을 대신하는 다양한 메서드를 제공한다.

- `BigInteger` : long으로도 다룰 수 없는 큰 범위 정수를 처리하기 위한 것

- `BigDecimal` : double로도 다룰 수 없는 큰 범위의 부동 소수점수를 처리하기 위한 것

```java
// Number클래스의 실제 소스 코드
public abstract class Number implements java.io.Serializable {
    public abstract int    intValue();
    public abstract long   longValue();
    public abstract float  floatValue();
    public abstract double doubleValue();
    
    public byte bytevalue(){
        return (byte)intValue();
    }
    public short shortValue(){
        return (short)shortValue();
    }
}
```

**문자열을 숫자로 변환하기**

```java
// 3가지 방법
int		i  = new Integer("100").intValue();	// floatValue(), longValue(), ...
int     i2 = Integer.parseInt("100");
int     i3 = Integer.valueOf("100");
```

| 문자열 -> 기본형(반환값 기본형)                              | 문자열 -> 래퍼 클래스(반환값 래퍼클래스)                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| byte      b = Byte.parseByte("100");<br />shrot    s = Short.parseShort("100");<br />int         i = Integer.parseInt("100");<br />long      l = Long.parseLong("100");<br />float      f = Float.parseFloat("3.14");<br />double d = Double.parseDouble("3.14"); | Byte      b = Byte.valueOf("100");<br />short     s = Short.valueOF("100");<br />Integer  i = Integer.valueOf("100");<br />Long      l = Long.valueOf("100");<br />Float      f = Float.valueOf("3.14");<br />Double d = Double.valueOf("3.14"); |

JDK1.5부터 도입된 `오토박싱(autoboxing)`기능 때문에 반환값이 기본형일 때와 래퍼 클래스일 떄의 차이가 없어짐

> 성능은 valueOf()가 조금 더 느리다.

```java
int  i = Intger.parseInt("100");								int  i = Integer.valueOf("100");
									<---------------------->
long l = Long.parseLong("100");									long l = Long.valueOf("100");
```

**문자열이 10진수가 아닌 다른 진법(radix)의 숫자일때 변환하는 메서드**

```java
static int parseInt(String s, int radix)	// 문자열 s를 radix진법으로 인식
static Integer valueOf(String s, int radix)
    
    
// 예시
int i4 = Integer.parseInt("100",2);	// 100(2) -> 4
int i5 = Integer.parseInt("100",8);	// 100(8) -> 64
int i6 = Integer.parseInt("100",16); // 100(16)-> 256
int i7 = Integer.parseInt("FF",16);  // FF(16) -> 255
// 진법을 생략하면 10진수로 간주하기 때문
// int i8 = Intger.parseInt("FF");	// NumberFormatException발생

Integer i9 = Integer.valueOf("100",2);
Integer i10 = Integer.valueOf("100",8);
Integer i11 = Integer.valueOf("100",16);
Integer i12 = Integer.valueOf("FF",16);
```

**오토박싱 & 언박싱(autoboxing & unboxing)**

`오토박싱(autoboxing)` : 기본형 값을 래퍼 클래스의 객체로 자동 변환해주는 것

`언박싱(unboxing)` : 반대로 변환하는 것

JDK1.5 이전에는 기본형 참조형 간의 연산이 불가능했기에 래퍼 클래스로 기본형을 객체를 만들어서 연산해야 했다.

```java
int i = 5;
Integer iObj = new Integer(7);
int sum = i+ Obj;	// 에러. 기본형과 참조형 간의 덧셈 불가(JDK1.5이전)
```

JDK1.5이후 부터는 기본형과 참조형 간의 덧셈 가능. 

컴파일러가 자동으로 변환하는 코드를 넣어주기 때문이다.(아래 경우 컴파일러가 Integer객체를 int타입의 값으로 변환해주는 intValue()를 추가해줌)

| 컴파일 전의 코드                                             | 컴파일 후의 코드                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| int i =5;<br />Integer iObj = new Integer(7);<br /><br />int sum = i + iObj; | int i = 5;<br />Integer iObj = new Integer(7);<br /><br />int sum = i + iObj.intValue(); |

```java
Integer ten = 10;	// 자동 박싱. Integer ten = Integer.valueOf(10);로 자동처리 됨
int n = ten;		// 자동 언박싱. int n = ten.intValue();로 자동 처리됨
System.out.println(ten+n);	// ten.intValue() + n; 
```

> 이 외에도 내부적으로 객체 배열을 가지고 있는 Vector클래스나 ArrayList클래스에 기본형 값을 저장해야할 때나 형변환이 필요할 때도 컴파일러가 자동적으로 코드를 추가해준다.
>
> ```java
> ArrayList<Integer> list = new ArrayList<integer>();
> list.add(10);			    // 오토박싱. 10 -> new Integer(10)
> int value  =  list.get(0);	// 언박싱. new Integer(10) -> 10
> ```

## 2. 유용한 클래스

### 2.1 java.util.Objects클래스

> 모두 import java.util.Objects를 해야 사용가능

Object클래스의 보조 클래스로 모든 메서드가 `static`이다. 객체의 비교나 널 체크(null check)에 유용하다.

`isNull()`

```java
static boolean isNull(Object obj)	// 해당 객체가 널이면 true, 아니면 false
// 예시
System.out.println("isNull(null) ="+Objects.isNull(null)); 		// isNull(null) =true
```

`nonNull()`

```java
static boolean nonNull(Object obj)	// 해당 객체가 널이면 false, 아니면 true
// 예시
System.out.println("nonNull(null) ="+Objects.nonNull(null));	// nonNull(null) =false
```

`requireNonNull()` 

```java
// 해당 객체가 널이 아니어야 하는 경우에 사용
// 만약 널이면, NullPointException을 발생시킨다.
static <T> T requireNonNull(T obj)
static <T> T requireNonNull(T obj, String message)	// 두 번째 매개변수로 지정하는 문자열은 예외의 메세지이다.
static <T> T requireNonNull(T obj, Supplier<String> messageSupplier)
```

매개변수의 유효성 검사를 다음과 같이 했는데, 이제는 requireNonNull()의 호출만으로 간단히 끝낼수 있음

```java
void setName(String name){
	if(name == null)
		throw new NullPointerException("name must not be null");
	
	this.name = name;
}
											|
                                            |
                                            v
void setName(String name){
	this.name = Objects.requireNonNull(name, "name must not be null");                                                
}
```

`compare()` : 대소비교를 한다.

```java
// 두 비교대상이 같으면 0, 크면 양수, 작으면 음수 반환
static int compare(Object a, Object b, Comparator c)

// 예시
import java.util.Comparator;
Comparator c = String.CASE_INSENSITIVE_ORDER; // 대소문자 구분 안하는 비교    
System.out.println("compare(\"aa\",\"bb\")="+Objects.compare("aa","bb",c)); 	// -1
System.out.println("compare(\"bb\",\"aa\")="+Objects.compare("bb","aa",c));		// 1
System.out.println("compare(\"ab\",\"AB\")="+Objects.compare("ab","AB",c));		// 0
```

`equals()`

Object클래스에 정의된 equals()는 객체를 비교할때등 null인지 반드시 확인해야 하지만(null값을 비교하려 하면 NullPointException이 발생하기 때문에),  Objects의 equals()는 null검사를 하지 않아도 된다.

```java
String a = null;
String b = "aa";

if(a!= null && a.equals(b)) {
	...
}
			|
			|
			V
if(Objects.equals(a,b)){
	...
}
```

equals() 메서드의 실제 코드

```java
// eqauls()내부에서 a와 b의 널 검사를 하기에 널 검사를 위한 조건식을 따로 넣지 않아도 된다.
// a와 b가 모두 널일 경우에는 참을 반환한다.

public static booelan equals(Object a, Object b){
	return (a==b) || (a!=null && a.equals(b));
}
```

`deepEquals()` : 객체를 재귀적으로 비교하기 때문에 다차원 배열의 비교도 가능

```java
String [][] str2D = new String [][]{{"aaa","bbb"},{"AAA","BBB"}};
String [][] str2D2 = new String [][]{{"aaa","bbb"},{"AAA","BBB"}};

System.out.println(Objects.eqauls(str2D, str2D2));	// false;
System.out.println(Objects.deepEqauls(str2D, str2D2));	// true;
```

`toString()`: equals처럼 내부적으로 null 검사를 한다.

```java
static String toString(Object o)

// o가 널일 때, 대신 사용할 값 지정
static String toString(Object o, String nullDefalut)
    
// 예시
System.out.println("toString(null)="+Objects.toString(null));	// toString(null)=null
System.out.println("toString(null,\"\")="+Objects.toString(null,""));	// toString(null,"")=
```

`hashCode()`: 이 역시 내부적으로 널 검사를 한후에 Object클래스의 hashCode()를 호출한다

```java
static int hashCode(Object o)
static int hash(Object...values)

// 예시
System.out.println("hashCode(null)="+Objects.hashCode(null));    //	hashCode(null)=0
```

### 2.2 java.util.Random클래스

> 모두 import java.util.Random를 해야 사용가능

난수 생성법

1. Math.random() : 내부적으로 Radnom()클래스의 인스턴스를 생성해서 사용한다.
2. Random클래스를 사용

```java
// 아래의 두 문장은 동등하다.
double randNum = Math.random();
double randNum = new Random().nextDobule();	
```

1~6사이의 정수를 난수로 얻고자 할 때

```java
int num = (int)(Math.random() * 6)+1;
int num = new Random().nextInt(6) + 1; // nextInt(6)은 0~5사이의 정수를 반환
```

**Random클래스의 생성자와 메서드**

Math.random과 Random()의 가장 큰 차이점 : Random()은 `종자값(seed)` 을 설정할 수 있다. 종자값이 같은 **Radnom인스턴스들은 항상** **같은 난수를 같은 순서대로 반환한다.**

```java
// Random() 생성자는 아래와 같이 종자값을 System.currentTimeMillis()로 하기 때문에 실행할때마다 얻는 난수가 달라짐

public Random(){
	this(System.currentTimeMillis());	// Random(long seed)를 호출한다.
}
```

seed값 설정 예제

```java
import java.util.Random;

public class RandomEx1 {
    public static void main(String[] args) {
        // rand, rand2 모두 seed를 1로 설정
        Random rand = new Random(1);
        Random rand2 = new Random(1);

        System.out.println("= rand =");
        for(int i=0;i<5;i++)
            System.out.println(i+ ":"+rand.nextInt());
        
        System.out.println();
        System.out.println("= rand2 =");
        for(int i=0;i<5;i++)
            System.out.println(i+ ":"+rand2.nextInt());
    }
}
/* 실행결과
= rand =
0:-1155869325
1:431529176
2:1761283695
3:1749940626
4:892128508

= rand2 =
0:-1155869325
1:431529176
2:1761283695
3:1749940626
4:892128508
*/
```

Random의 생성자와 메서드들

| 메서드                        | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| Random()                      | 현재시간(System.currentTimeMillis())을 종자값(seed)으로 이용 하는 Random 인스턴스를 생성한다 |
| Random(long seed)             | 매개변수seed를 종자값으로 하는 Random 인스턴스를 생성한다    |
| boolean nextBoolean()         | boolean타입의 난수를 반환한다.                               |
| void nextBytes(byte [] bytes) | bytes 배열에 byte타입의 난수를 채워서 반환한다.              |
| double nextDouble()           | double타입의 난수를 반환한다.(0.0<= x < 1.0)                 |
| float nextFloat()             | float타입의 난수를 반환한다.(0.0<= x < 1.0)                  |
| double nextGaussian()         | 평균은 0.0이고 표준편차는 1.0인 가우시안(Gaussian)분포에 따른 double형의 난수를 반환 한다. |
| int nextInt()                 | int타입의 난수를 반환한다.(int의 범위)                       |
| int nextInt(int n)            | 0~n의 범위에 있는 int값을 반환한다.(n은 범위에 포함되지 않음) |
| long nextLong()               | long타입의 난수를 반환한다.(long의 범위)                     |
| void setSeed(long seed)       | 종자값을 주어진 값(seed)으로 변경한다.                       |

0~9 사이의 난수를 100개 발생시키고 각 숫자의 빈도수를 센 다음 그래프를 그리는 예제

```java
import java.util.Random;

public class RandomEx2 {
    public static void main(String[] args) {
        Random rand = new Random();
        int[] number = new int[100];
        int[] counter = new int[10];

        for (int i = 0; i < number.length; i++) {
            //    System.out.print(number[i]=(int)(Math.random()*10));
            System.out.print(number[i] = rand.nextInt(10));
        }
        System.out.println();

        for (int i = 0; i < number.length; i++)
            counter[number[i]]++;

        for (int i = 0; i < counter.length; i++)
            System.out.println(i + "의 개수 : " + printGraph('#', counter[i]) + " " + counter[i]);
    }

    public static String printGraph(char ch, int value) {
        char[] bar = new char[value];

        for (int i = 0; i < bar.length; i++)
            bar[i] = ch;

        return new String(bar);
    }
}
/* 실행결과
6471911944770946713851869712722137427481547110261566622547307976836894136447697217113367159350314377
0의 개수 : #### 4
1의 개수 : ################# 17
2의 개수 : ######## 8
3의 개수 : ########## 10
4의 개수 : ############ 12
5의 개수 : ###### 6
6의 개수 : ############ 12
7의 개수 : ################## 18
8의 개수 : ##### 5
9의 개수 : ######## 8
*/
```

Math.random()을 이용해서 실제 프로그래밍에 유용한 메서드 만들기

```java
// 배열 arr을 from과 to범위의 값들로 채워서 반환한다.
public static int[] fillRand(int[] arr, int from, int to) {
       for (int i = 0; i < arr.length; i++)
           arr[i] = getRand(from, to);
       return arr;
}

// 배열 arr을 배열 data에 있는 값들로 채워서 반환한다.
public static int[] fillRand(int[] arr, int[] data) {
    for (int i = 0; i < arr.length; i++)
        arr[i] = data[getRand(0, data.length - 1)];
    return arr;
}

// from과 to범위의 정수(int)값을 반환.from과 to모두 범위에 포함된다.
public static int getRand(int from, int to) {
    return (int) (Math.random() * (Math.abs(to - from) + 1)) + Math.min(from, to);
}

// 실행
public static void main(String[] args) {
    for (int i = 0; i < 10; i++)
        System.out.print(getRand(5, 10) + ",");
    System.out.println();

    int[] result = fillRand(new int[10], new int[]{2, 3, 7, 5});
    System.out.println(Arrays.toString(result));
}
/* 실행결과
8,9,9,8,6,8,6,7,5,6,
[2, 5, 2, 7, 5, 3, 5, 5, 2, 2]
*/
```

데이터베이스에 넣을 테스트 데이터를 만드는 예제

```java
class RandomEx4 {
    final static int RECORD_NUM = 10;   // 생성할 레코드의 수
    final static String TABLE_NAME = "TEST_TABLE";
    final static String[] CODE1 = {"010", "011", "017", "018", "019"};
    final static String[] CODE2 = {"남자", "여자"};
    final static String[] CODE3 = {"10대", "20대", "30대", "40대", "50대"};

    public static void main(String[] args) {
        for (int i = 0; i < RECORD_NUM; i++) {
            System.out.println(" INSERT INTO " + TABLE_NAME
                    + " VALUES ("
                    + " '" + getRandArr(CODE1) + "'"
                    + ", '" + getRandArr(CODE2) + "'"
                    + ", '" + getRandArr(CODE3) + "'"
                    + ", '" + getRand(100,200) // 100~200사이의 값을 얻는다.
                    + "); ");
        }
    }


    public static String getRandArr(String[] arr) {
        return arr[getRand(arr.length - 1)];  // 배열에 저장된 값 중 하나를 반환한다.
    }

    public static int getRand(int n) {
        return getRand(0, n);
    }

    public static int getRand(int from, int to) {
        return (int) (Math.random() * (Math.abs(to - from) + 1)) + Math.min(from, to);
    }
}
/* 실행결과
 INSERT INTO TEST_TABLE VALUES ( '018', '여자', '40대', '174); 
 INSERT INTO TEST_TABLE VALUES ( '019', '여자', '40대', '101); 
 INSERT INTO TEST_TABLE VALUES ( '017', '남자', '20대', '197); 
 INSERT INTO TEST_TABLE VALUES ( '010', '남자', '20대', '181); 
 INSERT INTO TEST_TABLE VALUES ( '019', '남자', '40대', '191); 
 INSERT INTO TEST_TABLE VALUES ( '011', '남자', '10대', '190); 
 INSERT INTO TEST_TABLE VALUES ( '011', '여자', '10대', '127); 
 INSERT INTO TEST_TABLE VALUES ( '018', '여자', '40대', '155); 
 INSERT INTO TEST_TABLE VALUES ( '019', '여자', '40대', '152); 
 INSERT INTO TEST_TABLE VALUES ( '010', '남자', '40대', '165); 
 */
```

