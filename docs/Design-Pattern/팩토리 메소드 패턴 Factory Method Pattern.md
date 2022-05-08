---
layout: default
title: 팩토리 메소드 패턴 Factory Method Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 101
last_modified_date: 2021-05-02 10:34:00
last_modified_at: 2021-05-02 10:34:00
---

# 팩토리 메소드 패턴 Factory Method Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

팩토리 메소드 패턴 (Factory Method Pattern)이란, 인스턴스를 생성하는 책임을 구체적인 클래스가 아닌 추상적인 인터페이스의 메서드(팩토리)에게 위임하기 위해 사용한다.

인스턴스 책임을 팩토리에 위임하는 이유는, 확장에 열려 있고 변경에 닫혀 있는 객체지향 원칙에 알맞도록 설계하기 위함 이다. 
<code>Car</code> 인터페이스를 구현한 <code>Benz</code> 와 <code>Bmw</code> 구현체가 있다고 해보자. 
<code>Benz</code> 객체를 생성하기 위해서는 <code>new Benz()</code> 를 통해 직접 생성할 수 있는데 이는 매우 직관적이지만 변경에 열려있는 상태이다.
<code>Benz</code> 를 생성하기 위한 파라미터가 변경되거나 객체의 이름이 변경될 때 마다 <code>new Benz()</code> 를 했던 모든 부분을 수정해야 한다.
더군다나 <code>Benz</code> <code>Bmw</code> 와 같이 비슷한 형태의 구조를 가진 구현체들이 추가 될 때마다 비슷한 중복 코드가 매번 반복될 것이다. 

이를 <code>Factory</code> 를 통해 인스턴스 생성을 위임 한다면 매번 객체를 직접 생성하지 않아도 되고 객체 생성이 변경되더라도 <code>Factory</code> 구현체 한곳에서만 변경하면 된다.
그리고 다양한 구현체가 확장 될 때마다 팩토리 구현체에서 추가하면 되고, 기존에 사용하는 비지니스 로직은 변경하지않고 재사용이 가능하다는 장점이 있다.  


### 장점

- 팩토리와 구현체 간의 루즈 커플링 덕분에, 구현체를 생성하는 코드를 건드리지 않고 그와 같은 종류의 새로운 구현체를 다른 방법으로 인스턴스를 생성할 수 있고 확장할 수 있다.
- 코드는 훨씬 더 간결해지고 기존 코드가 복잡해지지 않는다.

### 단점

- 클래스의 개수가 늘어나는 단점이 있다.

## 생성 예시

![factory-method-pattern.png](/meta/docs/design-pattern/factory-method-pattern-class-diagram.png)

**Car.java**
```java
public interface Car {
    String getBrand();
    String getModel();
}
```

**AbstractCar.java**
```java
public abstract class AbstractCar implements Car{
    protected String brand;
    protected String model;
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public void setModel(String model) {
        this.model = model;
    }
}
```

**Benz.java**
```java
public class Benz extends AbstractCar{
    public Benz(String model) {
        setBrand("benz");
        setModel(model);
    }
    @Override
    public String getBrand() {
        return this.brand;
    }
    @Override
    public String getModel() {
        return this.model;
    }
}
```

**Bmw.java**
```java
public class Bmw extends AbstractCar {
    public Bmw(String model) {
        setBrand("bmw");
        setModel(model);
    }
    @Override
    public String getBrand() {
        return this.brand;
    }
    @Override
    public String getModel() {
        return this.model;
    }
}
```
**CarFactory.java**
```java
public interface CarFactory {
    Car create(String model);
}
```

**BenzFactory.java**
```java
public class BenzFactory implements CarFactory {
    @Override
    public Car create(String model) {
        return new Benz(model);
    }
}
```

**BmwFactory.java**
```java
public class BmwFactory implements CarFactory {
    @Override
    public Car create(String model) {
        return new Bmw(model);
    }
}
```

**Main.java**
```java
public class Main {
    public static void main(String[] args) {
        Main main = new Main();
        Car benz = main.createCar(new BenzFactory(), "5-series");
        Car bmw = main.createCar(new BmwFactory(), "s-class");
    }
    public Car createCar(CarFactory carFactory, String model) {
        return carFactory.create(model);
    }
}
```

## 실전에서 사용된 예시

### JAVA

- Calendar

### SPRING

- BeanFactory

## 참고

팩토리 메소드 패턴에서 팩토리까지 추상화 개념을 더한것을 추상 팩토리 패턴이라고 한다.