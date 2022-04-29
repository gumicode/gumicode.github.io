---
layout: default
title: 팩토리 메소드 패턴 Factory Method Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 101
last_modified_date: 2021-04-30 04:08:00
last_modified_at: 2021-04-30 04:08:00
---

# 팩토리 메소드 패턴 Factory Method Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요
팩토리 메소드 패턴 (Factory Method Pattern)이란, 인스턴스를 생성하는 책임을 구체적인 클래스가 아닌 추상적인 인터페이스의 메서드로 감싸는 패턴이다.

## 사용 목적


다양한 구현체를 만들 수 있는 추상화된 팩토리를 제공하기 위함이다. 팩토리를 통해서 인스턴스 생성을함으로써 프로젝트 내부에서 사용하는 특정 구현체들의 인스턴스 모음을 관리할 수 있다. 

예를들어 자동차 라는 구현체를 만든다고 했을때 아래와 같이 다양한 클래스 (구현체) 들이 만들 수 있다.

- BMW 3 시리즈
- BMW 5 시리즈
- BMW 7 시리즈 
- 벤츠 C 클래스
- 벤츠 E 클래스
- 벤츠 S 클래스
- 테슬라 모델 S
- 테슬라 모델 3

공통 부분은 클래스 설계적인 관점에서 인터페이스나 추상클래스로 해결할 수 있다. 그러나 오해할 수 있는것이 팩토리 메소드 패턴은 설계 관점에서 공통화된 구현체를 묶는 목적이 아니라, 공통화된 구현체들의 인스턴스를 한곳에서 생성하기 위한 목적이다. 

즉, 팩토리(공장)에서 인스턴스 생성을 강제함으로써, 중간에 생성 방식이 변경될 때마다 구현체의 코드를 하나씩 수정하지 않아도 되며, 클라이언트는 수 많은 구현체를 모두 알 필요 없이 팩토리를 통한 객체 생성에만 집중할 수 있다.

추가적인 기능으로 스프링을 사용할 때 팩토리를 빈 객체로 사용한다면, 팩토리를 통해 생성된 인스턴스를 한곳에서 관리하는 기능을 추가하여 관리의 용이성을 확보하는 방법도 있다.

### 팩토리 메소드 장점

- 구현체의 코드를 건드리지 않고 팩토리를 통해 다양한 구현체를 생성할 수 있고 확장할 수 있다.

### 팩토리 메소드 단점

- 클래스의 개수가 늘어나는 단점이 있다.

## 생성 규칙

- 구현체는 팩토리를 통해서만 생성해야 한다.

### 생성 예시

```java
public interface Car {
    String getModel();
}
```

```java
public class Bmw implements Car {
    private String model;

    public Bmw(String model) {
        this.model = model;
    }
    public String getModel(){
        return this.model;
    }
}
```

```java
public class Benz implements Car {
    private String model;

    public Benz(String model) {
        this.model = model;
    }
    public String getModel(){
        return this.model;
    }
}
```

```java
public class Tesla implements Car {
    private String model;

    public Tesla(String model) {
        this.model = model;
    }
    public String getModel(){
        return this.model;
    }
}
```

```java
public class CarFactory {

    public static create(String brand, String model) {

        if("bmw".equals(brand)) {
            return new Bmw(model);
        }

        if("benz".equals(brand)) {
            return new Benz(model);
        }

        if("tesla".equals(brand)) {
            return new Tesla(model);
        }

        throw new IllegalArgumentException();
    }
}
```

```java
public class Example {
    public static void main(String[] args) {
        Car bmw3series = CarFactory.create("bmw", "3시리즈");
        Car bmw5series = CarFactory.create("bmw", "5시리즈");
        Car benzCClass = CarFactory.create("benz", "c-class");
        Car teslaModel3 = CarFactory.create("tesla", "model3");
    }
}
```

## 실전에서 사용된 예시

### JAVA
- java.util.Calendar#getInstance()

### SPRING
- BeanFactory

## 결론
팩토리 메소드 패턴은 아키텍처가 복잡할수록 유용하다는 장점이 있지만, 반대로 아키텍처가 단순할 경우 오히려 더 복잡성을 늘리는 결과를 초래할 수 있으므로 잘 판단하여 적용하는것이 좋다.