---
layout: default
title: JVM, JRE, JDK
parent: 자바 Java
nav_order: 200
last_modified_date: 2021-03-18 00:00:00
last_modified_at: 2021-03-18 00:00:00
---

# JVM, JRE, JDK
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

![jdk](/meta/docs/java/jdk.png)

## 개요

**JVM**(Java Virtual Machine) 자바 가상 머신

바이트코드를 OS에 특화된 기계어로 변환하여 실행하는 구현체이다. 쉽게 설명하자면 .class 파일로 된 바이트 코드를 OS가 이해할 수 있는 기계어로 번역후 CPU에 명령을 보내 실행하는 역할을 담당한다.

**JRE**(Java Runtime Enviroment) 자바 실행 환경

각각의 OS 에서 JVM 을 동작할수 있게 실행 환경을 구성해 놓은 도구이다.

**JRK**(Java Development Kit) 자바 개발 키트

자바코드를 바이트코드로 컴파일 해주는 기능이 추가된 것이 **JDK**이다.  

개발자들이 일반적으로 설치하는 도구이다. JDK 를 설치하면 JRE 와 JVM 이 모두 포함되어 있어 이것만 설치하면 컴파일 부터 실행까지 모든 기능을 사용할 수 있다.

JVM은 스펙을 따르기만 하면 어떤 벤더는 JVM 을 만들고 배포할수 있다. (참고)[https://docs.oracle.com/javase/specs/index.html] 단, JVM 자체를 독립적으로 배포하지는 않고 JDK 와 함께 통합으로 배포하고 있다.

**자바 유료화 논란**이 발생되기 이전은 대부분 오라클JDK 를 이용했다. java를 인수한 오라클이 만들기에 가장 신뢰할수 있었기 때문인데 java 11 부터 오라클jdk 유료화 소식이 전해지면서 대부분 유저들이 오라클jdk 에서 이탈하기 시작했다. 최근에는 OpenJDK, Adopt OpenJDK, Azul OpenJDK 를 많이 사용한다.

자바 코드는 2단계를 거쳐 실행 되기 때문에 1단계만 거치는 C,C++ 과 같은 언어보다 속도가 느리다고 하는 이유가 여기서 발생한다. 대신 자바에서 지원하는 OS 에서는 호환성 문제 없이 실행할 수 있다는 장점이 있다.

## JVM 구조

![jvm](/meta/docs/java/jvm.png)

### 클래스 로더 시스템 영역

.class 로 이루어진 바이트코드를 읽어 들여서 적절하게 메모리에 할당하는 역할을 담당한다. 클래스 로더 시스템을 아래 3단계로 구분할 수 있다.

#### 로딩

클래스를 읽어오는 과정

#### 링크

레퍼런스를 연결하는 과정

#### 초기화

static 값 초기화 및 변수에 할당

### 메모리 영역

메모리 영역에는 클래스 로더가 배치한 메모리를 읽어서 실행엔진에 전달하는 역할을 담당한다.아래 5 단계로 구분할 수 있다.

#### 스택

메소드를 호출했을때 실행되는 메소드 내의 메모리 영역이다. 해당 메소드에서 만들어진 변수들은 모두 스택 메모리에 저장된다. 레퍼런스 타입은 레퍼런스의 주소값만 스택에 저장되고 실제 구현된 메모리는 힙 영역에 저장된다. 

스레드 마다 하나씩 만들어 진다. 스레드 마다 runtime stack 이라는 것을 만들고 그 안에 스택 프레임을 만든다.

#### PC

스레드 마다 스레드 내 현재 실행할 스택 프레임을 가리키는 포인터를 의미한다.

스레드 마다 하나씩 만들어 진다.

#### 네이티브 메소드 스택

자바에서는 native 메소드를 지원하는데 쉽게말해 자바에서 C,C++ 를 실행하기 위한 메소드라고 보면 된다. 실제로 많은 자바 객체들이 native 메소드로 구현된 것이 많이 있다. 자바에서 직접 C 언어 관련 모듈을 실행하는것이 아니라면 사용할 일은 드물다. 

#### 힙

인스턴스로 생성한 객체를 저장하고 공유하는 영역이다. 레퍼런스 타입들은 모두 heap 영역에 저장된다.

스레드에 국한되지 않고 모든 영역에서 접근 가능한 공유된 영역이다.

#### 메소드

클래스이름, 부모클래스 이름, 메소드, 변수 등 클래스 수준의 정보를 저장하며, 메소드에 저장된 영역은 다른 자원들과 **공유**가 가능하다. 그렇기에 어디서 클래스를 만들어도 다른 패키지에서 해당 클래스가 존재하는지 여부를 알 수 있다. 물론 접근할 수 있는지 여부는 클래스 접근 지정자에 따라 달라진다.

스레드에 국한되지 않고 모든 영역에서 접근 가능한 공유된 영역이다.

### 실행 엔진 영역

#### 인터프리터

바이트 코드를 한줄식 실행한다.

#### JIT 컴파일러

인터프리터의 효율을 높이기 위해, 인터프리터가 반복되는 코드를 발견하면 JIT 컴파일러로 반복되는 코드를 모두 네이티브 코드로 바꿔둔다. 그 다음부터 인터프리터는 네이티브 코드로 컴파일 된 코드를 바로 사용한다.

#### GC (Garbage Collector)

더이상 참조되지 않는 객체를 모아서 정리한다.

## 자바 바이트코드

바이트코드를 만드는 순서는 다음과 같다. java 파일을 생성후 class 파일로 변환한다. class 파일은 bytecode 로 되어있기 때문이 txt 형식이 아니어서 우리가 눈으로 읽을 수 없으므로, javap 라는 역어셈블리 명령어를 통해 어떻게 코드가 해석되었는지 확인할 수 있다. 


**1. HelloJava.java 코드생성**
```java
public class HelloJava {
  public static void main(String args[]) {
    System.out.println("hello");
  }
}
```

**2. 바이트코드 변환**
```
javac HelloJava.java
```

**3. 바이트코드 확인**
```
javap -c HelloJava

Compiled from "HelloJava.java"
public class HelloJava {
  public HelloJava();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #13                 // String hello
       5: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```