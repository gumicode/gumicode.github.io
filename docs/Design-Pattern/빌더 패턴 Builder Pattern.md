---
layout: default
title: 빌더 패턴 Builder Pattern
parent: Design Pattern
nav_order: 103
last_modified_date: 2021-05-11 00:38:00
last_modified_at: 2021-05-11 00:38:00
---

# 빌더 패턴 Builder Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

빌더 패턴(Builder Pattern)이란,  다양한 구성으로 만들어지는 인스턴스를 동일한 프로세스를 통해 만들수 있도록 해주는 패턴이다. 

빌더 패턴은 별로 어렵지 않기 때문에 자세한 설명보다는 코드로 직접 구현해보면 누구나 쉽게 이해할 수 있다. 

### 장점

- 객체를 만드는 프로세스를 독립적으로 분리할 수 있다.
- 객체를 생성할때 파라미터 조건이 많거나 복잡할 수록 가독성을 높일 수 있다.

## 생성 방법

### 생성 예시 1 - 직접 구현

**CarBuilder.java**
```java
public interface CarBuilder {
    CarBuilder brand(String name);
    CarBuilder model(String model);
    Car getCar();
}
```

**Car.java**
```java
public class Car implements CarBuilder {
    private String brand;
    private String model;

    @Override
    public CarBuilder brand(String brand) {
        this.brand = brand;
        return this;
    }
    @Override
    public CarBuilder model(String model) {
        this.model = model;
        return this;
    }
    @Override
    public Car getCar(){
        Car car = new Car();
        car.brand = brand;
        car.model = model;
        return car;
    }
}
```

**Main.java**
```java
public class Main {
    public static void main(String[] args) {
        CarBuilder carBuilder = new Car();
        Car car = carBuilder
                .brand("brand")
                .model("model")
                .getCar();
    }
}
```

### 생성 예시 2 - Lombok 활용

**Car.java**
```java
@Builder
public class Car {
    private String brand;
    private String model;
}
```

```java
public class Main {
    public static void main(String[] args) {
        CarBuilder carBuilder = new Car();
        Car car = Car.builder()
            .brand("brand")
            .model("model")
            .build();
    }
}
```