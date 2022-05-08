---
layout: default
title: 팩토리 메소드 패턴 Factory Method Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 101
last_modified_date: 2021-05-08 22:49:00
last_modified_at: 2021-05-08 22:49:00
---

# 팩토리 메소드 패턴 Factory Method Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

팩토리 메소드 패턴 (Factory Method Pattern)이란, 클라이언트가 객체를 직접 생성하지 않고 추상적인 인터페이스의 메서드(팩토리)에게 역할을 위힘하는 패턴이다.

객체의 인스턴스 생성 책임을 팩토리에 위임하는 이유는, 확장에 열려 있고 변경에 닫혀 있는 객체지향 원칙에 알맞도록 설계하기 위함 이다.

만약 특정 객체를 직접 <code>new</code> 로 할당하여 사용하고 있다면, 이후 특정 객체가 변경되면 객체를 사용하고 있는 모든 코드를 수정해야 한다. 
그러나 팩토리에 이를 위임했다면, 객체가 직접적으로 변경 되더라도 실제 코드는 팩토리 구현체 1곳 에서만 변경하면 된다.
그리고 다양한 구현체가 확장 될 때마다 팩토리 구현체에서 추가하면 되고, 기존에 사용하는 비지니스 로직은 변경하지않고 재사용이 가능하다는 장점이 있다.  


### 장점

- 팩토리와 구현체 간의 루즈 커플링 덕분에, 구현체를 생성하는 코드를 건드리지 않고 그와 같은 종류의 새로운 구현체를 다른 방법으로 인스턴스를 생성할 수 있고 확장할 수 있다.
- 클라이언트는 어떤 객체가 생성될 것인지 알 필요가 없다.
- 팩토리는 인터페이스로 추상화 되어 구현체 팩토리의 변경을 유연하게 할수 있다.

### 단점

- 클래스의 개수가 늘어나는 단점이 있다.

## 생성 예시

[![factory-method-pattern.png](/meta/docs/design-pattern/factory-method-pattern-class-diagram.png)](/meta/docs/design-pattern/factory-method-pattern-class-diagram.png){: target="_blank"}

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
        CarFactory benzFactory = new BenzFactory();
        benzFactory.create("5-series");

        CarFactory bmwFactory = new BmwFactory();
        benzFactory.create("s-class");
    }
}
```

## 실전에서 사용된 예시

### JAVA

- Calendar

### SPRING

- BeanFactory

## 참고사항

팩토리 메소드 패턴에서 팩토리까지 추상화 개념을 확장한 것을 추상 팩토리 패턴이라고 한다.