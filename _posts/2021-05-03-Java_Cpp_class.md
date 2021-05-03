---
title: Java와 C++의 class 차이점 정리
date: 2021-05-03 23:05:00 +0900
categories: [Language, Java]
tags: [class]
toc: true
---

Class에 관해 Java와 C++의 차이점을 간단하게 표로 정리하면 아래와 같습니다.

| Language | C++ | Java |
|:--:|:--:|:--:|
| 상속 관계 | public, private, protected | extends, implement |
| Virtual method | 필요시 명시 | Default |
| 조부모 class 접근 | 연산자를 통해 가능 | 부모 class를 통해서만 가능 |

___

## extends

Extend는 부모 class의 내용을 자식 클래스에게 상속하는 기본 상속 방법입니다.  

```java
public class Car {
    String name;

    public void setName(String name) {
        this.name = name;
    }
}
public class Avante extends Car {

}
```

extends를 사용하여 상속을 받으면 Is-A 관계를 가지며 C++에서는 public으로 상속 받는 것과 동일한 효과를 가집니다. (자식클래스 is a 부모클래스)
이 관계는 자식클래스를 부모클래스인 것 처럼 사용 할 수 있습니다.

```java
Avante custom_car = new Car();
```

모든 java class들은 사실은 Object class의 상속을 받습니다. 생략된것을 나타내면 아래와 같습니다.

```java
public class Car extends Object {
    ...
}
```

## Interface

인터페이스는 일종의 다중 상속을 대체하는 개념으로 이해하면 됩니다. 일반적으로 Interface class를 정의 할 때 Member method에는 class의 정의만 있고 내용은 존재하지 않습니다.

```java
public interface Fuel {
    public String getFeul();
}
```

또한 interface를 상속 받을 때 ```implement```을 사용하여 상속받습니다.

```java
public class Avante extends Car implements Fuel {
    public String getFeul();
}
```

## Abstract

추상 클래스(Abstract class)는 Interface의 역할도 하면서 구현체의 역할도 동시에 하는 클래스입니다. Interface class에서의 method 정의만 하도록 설정이 가능하면서 필요 할 시에는 내용까지도 작성이 가능합니다.

```java
public abstract class Car extends Object{
    public String getFuel() {
        ...
    }
}
public class Avante extends Fuel {
    ...
}
```

## Overriding

Class 상속 관계에서 부모 클래스의 Method를 Overrriding 하여 method를 재 정의 할 수 있습니다. 위의 상속관계에서 Method Overrriding을 나타내면

```java
public class Avante extends Car {
    public void setName(String name) {
        this.name = name + "Kim";
    }
}
```

기존에 suer.setName Method에서는 매개변수 name을 저장하는데 반해 Overriding하여 setName은 name 변수에 매개변수 name에 Kim을 합쳐서 저장합니다.

## Overloding

Overriding은 덮어 쓰는 개념이라면 Overloding은 매개변수를 달리 하는 메소드 '추가'의 개념입니다.

```java
public class Avante extends Car {
    public void setName(String name, String lastName) {
        this.name = name + lastName;
    }
    
    public static main(String[] args) {
        Avante customCar = new Avante();
        customCar.setName("아반떼");
        customCar.setName("아반떼", "현대");
    }
}
```

매개변수 한개였던 Car class의 setName Method에서 두개로 추가하여 Overloding하였다. 이 때 기존에 사용하던 ```setName(String name)```또한 사용 가능합니다.

___

## Reference

* [점프 투 자바](https://wikidocs.net/280)
