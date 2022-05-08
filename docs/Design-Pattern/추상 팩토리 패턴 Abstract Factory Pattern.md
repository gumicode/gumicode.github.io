---
layout: default
title: 추상 팩토리 패턴 Abstract Factory Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 102
last_modified_date: 2021-05-09 01:53:00
last_modified_at: 2021-05-09 01:53:00
---

# 추상 팩토리 패턴 Abstract Factory Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

추상 팩토리 패턴 (Abstract Factory Pattern)이란, 관련있는 여러 인스턴스를 만들어주는 팩토리를 추상화된 형태로 정의하는 패턴이다.

두개의 패턴이 굉장히 구조가 비슷하다. 이는 gof 가 정의한 디자인 패턴의 특징이기도 한데, 패턴들끼리 서로 비슷비슷하게 생긴것들이 많다. 이 두개의 패턴의 가장큰 차이점은 관점이 다르기 때문이다. 

### 팩토리 메소드 패턴과 공통점

- 구체적인 객체 생성 과정을 추상화한 인터페이스를 제공한다.

### 팩토리 메소드 패턴과 차이점

- 관점이 다르다. 
- 팩토리 매소드 패턴은 팩토리를 구현하는 방법에 초점을 둔다.
- 추상 팩토리 패턴은 팩토리를 사용하는 방법에 초점을 둔다.
- 팩토리 메소드 패턴은 구체적인 객체 생성 과정을 구체적인 팩토리 클래스로 옮기는것이 목적
- 추상 팩토리 패턴은 관련있는 여러 객체를 생성해 주는 팩토리를 추상화하여 사용할 수 있도록 하는것이 목적


## 생성 예시

[![abstract-factory-patten](/meta/docs/design-pattern/abstract-factory-pattern-class-diagram.png)](/meta/docs/design-pattern/abstract-factory-pattern-class-diagram.png){: target="_blank"}

**CarModel.java**
```java
public interface CarModel {}
```

**BenzEClass.java**
```java
public class BenzEClass implements CarModel {}
```

**bmw5Series.java**
```java
public class bmw5Series implements CarModel {}
```


**Car.java**
```java
public interface Car {
    String getBrand();
    CarModel getModel();
}
```

**AbstractCar.java**
```java
public abstract class AbstractCar implements Car{
    protected String brand;
    protected CarModel model;
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public void setModel(CarModel model) {
        this.model = model;
    }
}
```

**Benz.java**
```java
public class Benz extends AbstractCar{
    public Benz(CarModel model) {
        setBrand("benz");
        setModel(model);
    }
    @Override
    public String getBrand() {
        return this.brand;
    }
    @Override
    public CarModel getModel() {
        return this.model;
    }
}
```

**Bmw.java**
```java
public class Bmw extends AbstractCar {
    public Bmw(CarModel model) {
        setBrand("bmw");
        setModel(model);
    }
    @Override
    public String getBrand() {
        return this.brand;
    }
    @Override
    public CarModel getModel() {
        return this.model;
    }
}
```
**CarFactory.java**
```java
public interface CarFactory {
    Car create(CarModel model);
}
```

**BenzFactory.java**
```java
public class BenzFactory implements CarFactory {
    @Override
    public Car create(CarModel model) {
        return new Benz(model);
    }
}
```

**BmwFactory.java**
```java
public class BmwFactory implements CarFactory {
    @Override
    public Car create(CarModel model) {
        return new Bmw(model);
    }
}
```

**Main.java**
```java
public class Main {
    public static void main(String[] args) {
        CarFactory benzFactory = new BenzFactory();
        Car benzEClass = benzFactory.create(new BenzEClass());

        CarFactory bmwFactory = new BmwFactory();
        Car bmw5Series = bmwFactory.create(new Bmw5Series());

        // 구현체는 다르지만 연관되어 있는 CarModel 를 추상화 하여 사용할 수 있음
        CarModel model1 = benzEClass.getModel();
        CarModel model2 = bmw5Series.getModel();
    }
}
```

## 실전에서 사용된 예시

### JAVA

- XPathFactory
- TransformerFactory
- DocumentBuilderFactory

### SPRING

- FactoryBean

## 참고

