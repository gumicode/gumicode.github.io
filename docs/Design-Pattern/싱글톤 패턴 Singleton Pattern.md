---
layout: default
title: 싱글톤 패턴 Singleton Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 100
last_modified_date: 2021-02-18 00:00:00
last_modified_at: 2021-02-18 00:00:00
---

# 싱글톤 패턴 Singleton Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

싱글톤 패턴 (Singleton Pattern)이란, 고유한 하나의 인스턴스만 제공 해 주는 클래스다.

## 사용 목적

어디에서 접근하던지, 동일한 객체의 동일한 데이터를 참조하기 위함이다. 

게임으로 비유하자면 환경설정 데이터를 싱글톤 객체로 관리하는것이 바람직할 것이다. 일반적으로 게임 설정에 들어 가 보면 해상도 설정, 사운드크기 등 다양한 옵션 설정값을 변경할 수 있는데, 이 변경된 값을 어느 곳에서든지 고유한 값을 참조할 수 있어야 올바른 결과를 응답할 수 있을 것이다. 만약 환경 설정 데이터가 매번 새로운 인스턴스로 생성 된다면 문제가 될 것이다. 이 처럼 매번 인스턴스를 새로 생성해야 하는 클래스가 아닌, 고유의 인스턴스 1개만 존재할 필요가 있는 객체의 경우 싱글톤 패턴으로 구현하는것이 바람직하다.

## 생성 규칙

설명을 간결하게 하기 위하여 JAVA 언어 기준으로 설명한다.

-  <code>new</code> 를 통한 인스턴스 생성이 불가능하게 만들어야 한다.
- 글로벌 하게 접근할 수 있는 방법을 제공해야 한다.


### 생성 예시 1

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if(instance == null) {
            this.instance = new Singleton();
        }
        return  instance;
    }
}
```

가장 기본적인 싱글톤 패턴 생성 방법이다. 다만 이 경우 **멀티 쓰레드** 환경에서 안전하지 않다는 문제점이 있다. 그 이유는 두개 이상의 쓰레드가 거의 동시에 접근을 했는데 아직 <code>this.instance = new Singleton();</code> 코드를 실행하지 않았던 상태라면 두개의 쓰레드가 <code>if(instance == null)</code> 를 모두 통과 하기 때문에 각 쓰레드별로 서로 다른 두개의 인스턴스가 생겨벼릴 수 있기 때문이다.

### 생성 예시 2

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if(instance == null) {
            this.instance = new Singleton();
        }
        return  instance;
    }
}
```

1번 예시와 차이점은 <code>synchronized</code> 메소드가 추가 됬다. 이러면 멀티 쓰레드에서 접근 하더라도 해당 메소드에 접근시 lock이 걸려 있기 때문에 메소드 수행이 끝날때 까지 대기 상태에 놓이게 된다. 구현하기 간편하지만 성능을 희생하는 방법이기에 '좋은코드' 라고 할 수는 없을 것이다.

### 생성 예시 3

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return this.INSTANCE;
    }
}
```

멀티 쓰레드 환경에서는 <code>if</code> 와 같은 조건문을 특히 주의해야 한다. 멀티 쓰레드는 거의 동시에 접근할 수 있고 순서를 보장 받을수 없기 때문이다. 1번 예시에서 사용했던 문제의 <code>if(instance == null)</code> 문을 완전히 삭제 하였다.

일반적인 환경에서 이대로 사용하여도 크게 문제는 없다. 다만 성능 최적화 측면에서 좋은 코드는 아니다. 멤버변수가 static 으로 구현되어 있기 때문에 굳이 저 싱글톤 객체를 호출 하지 않더라도 빌드 단계에서 이미 메모리를 점유하고 있기 때문이다. 싱글톤 객체를 반드시 호출하여 사용한다는 보장이 없는 상태에서 강제로 메모리를 점유하고 있는건 '좋은코드' 라고 하기에는 어려울 것이다.

일반적으로 사용이 될 때 호출 하는것이 최적화 측면에서 옳은 방향일 것이다. 다만, 객체 자체가 매우 용량이 커서 생성 하는데 시간이 매우 걸리는 예외가 존재 한다면 그때는 반대로 미리 빌드 단계에서 미리 생성해 놓는것도 하나의 선택지가 될 수는 있다. 결국 내가 '의도한' 것이라면 상관은 없다. 

### 생성 예시 4 (권장)

```java
public class Singleton {
    private Singleton() {}
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

Inner Class 를 활용한 방법이고 권장하는 방법이다. <code>getInstance</code> 가 호출 될때 Singleton 인스턴스를 생성하기에 성능최적화 면에서도 우수하며 멀티 쓰레드 환경에서도 안전하다. 이정도면 '좋은코드' 라고 할 수 있겠다. 


## 싱글톤 패턴 무력화 시키기

여기서부터는 심화 기능이다. 학습을 해도 좋고 하지 않아도 좋다. 위에서 언급한 4번째 예시 정도로 사용해도 충분하다. 하지만 지금부터 소개하는 기능들은 예시 4번으로 구현 하더라도 무력화 할수 있는 방법을 소개하고 그것을 대응할 수 있는 방법을 소개할 것이다. 그러나 악의적으로 사용하지 않는 이상 아래처럼 굳이 이상한 방식으로 코드를 작성하는 개발자는 없으므로 이런 경우가 있다 정도만 이해하면 좋을 것이다. 

### 직렬화, 역직렬화를 통하여 새로운 인스턴스 생성하기

직렬화를 통해 파일로 저장후 다시 그 파일을 읽어와 객체를 생성한다면, 다른 인스턴스를 생성할 수 있다.

```java
public class Singleton implements Serializable {
    ...
```
싱글톤 객체에 <code>implements Serializable </code> 직렬화 인터페이스를 추가한다.

```java
public static void main(String[] args) throws IOException, ClassNotFoundException {
        Singleton singleton1 = Singleton.getInstance();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream("single"));
        objectOutputStream.writeObject(singleton1);
        ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("single"));
        Singleton singleton2 = (Singleton) objectInputStream.readObject();
        System.out.println(singleton1 == singleton2); // false
    }
```

위와 같이 직렬화 역직렬화 기능을 사용하면 싱글톤 객체라고 하더라도 새로운 인스턴스를 생성할 수 있게 되어 싱글톤을 무력화 시킬 수 있다.

#### 대응 방법 1

직렬화 할 수 없도록 <code> implements Serializable </code> 사용을 하지 않는다.

#### 대응 방법 2

```java
public class Singleton implements Serializable {
    private Singleton() {}
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
    protected Object readResolve(){
        return getInstance();
    }
}
```

역직렬화 할 때 호출하는 메소드인 <code>readResolve()</code> 를 오버라이딩 해서 새로운 객체 생성을 미연에 방지한다. 그러면 역직렬화 하더라도 새로운 인스턴스가 아닌 기존 싱글톤 인스턴스를 가져올 것이다.

### 자바 리플렉션으로 인스턴스 생성하기

```java
public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Singleton singleton1 = Singleton.getInstance(); // 정상적인 인스턴스 생성
        Constructor<Singleton> declaredConstructor = Singleton.class.getDeclaredConstructor();
        declaredConstructor.setAccessible(true);
        Singleton singleton2 = declaredConstructor.newInstance(); // 비 정상적으로 새로운 인스턴스 생성
        System.out.println(singleton1 == singleton2); // false 
    }
```
JAVA 기본 문법중 자바 리플렉션 이라는 기능이 있다. 우리가 아무리 private 으로 외부에서 접근을 못하게 막아둔다고 하더라도 자바 리플렉션 기능 때문에 이를 쉽게 무력화할 수 있다. 위 예시를 보면 싱글톤으로 구현된 객체라서 새로운 인스턴스 생성이 불가능함에도 불구하고 정상적인 자바 문법을 사용하여 새로운 인스턴스를 만들어 버렸다. 악의적인 의도로 사용할 목적이 아니라면 이렇게 까지 일부러 코드를 작성하는 개발자는 없겠지만, 악의적인 목적을 완전히 배제할 수 없기에 자바 리플렉션을 대응할 수 있는 추가적인 방법이 무엇이 있을지 생각해 보는것도 필요하다.

#### 대응 방법

class 객체가 아닌 enum 객체로 싱글톤을 구현하면 된다. 이 방법은 첫번째 문제였던 직렬화 역직렬화 방식도 대응 가능하다.

```java
public enum Singleton {
    INSTANCE
}
```

```java
public static void main(String[] args) {
    Singleton singleton1 = Singleton.INSTANCE;
}
```

자바의 enum 은 직접 생성한 커스텀 메소드를 사용할 수 있기 때문에 우리가 원하는 유일한 단 하나의 인스턴스를 생성하고 데이터를 가져올 수 있다. 하지만 단점도 분명히 있다. 1. 빌드 단계에서 무조건 메모리를 점유할 수 밖에 없으며, 2 클래스 상속 기능을 활용할 수 없다는 단점이 있다.  1번 문제의 단점은 예시 3번에서 발생했던것과 동일한 문제다. 

## 결론

예시 4번과 같은 방법으로 싱글톤 객체를 구현하는것이 현 시점에서는 가장 권장되는 방식이다. enum 으로 만드는 방법 또한 참신한 방법이긴 하지만 위에 언급한 단점이 있다. 무엇보다도 무력화 코드의 경우 사실상 '실수'로 인하여 발생될 여지는 전혀 없다고 봐도 무방 하므로, 저 실수를 방지하기 위하여 미리 저렇게 대응까지 한다는건 너무 과한 방법이 아닐까 한다. 다만 언제나 무력화로 싱글톤을 풀어 버릴수 있는 가능성에 대해서는 항상 인지하고 있으면 될 것 같다.