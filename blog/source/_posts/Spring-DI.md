---
title: 스프링 삼각형 - DI (Dependency Injection)
date: 2018-01-01 19:48:13
clearReading: true
metaAlignment: center
keywords:
  - Java
  - DI
  - Spring

tags:
  - DI
  - Spring
  - Java
---

스프링 삼각형 중 DI 에 대해 알아보자
<!-- excerpt -->

---

## 의존성

### 1. 변수에 값을 할당하는 모든 곳에 의존 관계가 생긴다.

1. 대입 연산자 `=` 에 의해 변수에 값이 할당되는 순간에 의존이 생긴다.
2. 변수가 지역 변수이건, 속성이건, 할당되는 값이 리터럴이건 객체이건 의존은 발생한다.
3. 의존 대상이 내부에 있을 수도 있고, 외부에 있을 수도 있다.
(가깝게는 같은 파일 안에서, 또는 전역 변수, 멀리서는 같은 패키지의 다른 클래스, 더 멀리서는 외부 설정 파일 등)

DI는 외부에 있는 의존 대상을 주입하는 것을 말한다.

### 2. 의존 하는 / 의존 되는 관계에서 서로 다른 수명 주기를 가질 수 있다.

서로 다른 생명 주기는 집합 관계 / 구성 관계로 구분된다.

#### 집합 관계

각 부분이 없어도 전체가 의미 있는 경우
같은 의도로 이루어진 요소들이 한 개 이상 모인 경우

1. 부분이 전체와 **다른** 생명 주기를 가질 수 있다.
2. 각 부분이 전체와 **독립적**이다.
3. 각 부분이 또 다른 부분과 **독립적**이다.

ex) [ 집-냉장고, 사무실-책상, 필통-연필 ]

#### 구성 관계

각 부분이 **모두** 모여야 비로소 전체가 의미 있는 경우

1. 부분은 전체와 **같은** 생명 주기를 가진다.
2. 각 부분은 전체에 **종속적**이다.
3. 전체는 각 부분에 **종속적**이다.

ex) [ 사람-심장, 자동차-엔진, 프린터-잉크 ]


### 3. 의존 대상을 구현하고 배치할 때 응집도는 높이고 결합도는 낮추라는 기본 원칙에 충실하라.

대전제 : 코드는 변한다 

-> 편하게 일하자 
-> 요청 사항에 의해 코드가 변경되는 양을 낮추자 
-> 응집도는 올리고 결합도는 낮추자
-> SOILD를 준수하자
-> 디자인패턴으로 실천하자
-> 리팩토링으로 구체화하자


---

## 의존성 낮추기

### 기본 대상

독자가 책을 읽고, 그 책은 페이지로 이루어져 있으며, 페이지에는 여러 레이아웃이 있다고 가정한다.

``` java
  interface Page {
    String getLayout();
  }
```

``` java 
  public class ContentsPage implements Page {
    public String getLayout() {
      return "contents";
    }
  }
```

``` java
  public class ImagePage implements Page {
    public String getLayout() {
      return "image";
    }
  }
```


``` java
  public class Book {
    Page page;

    public Book() {
      this.page = new ContentsPage();
    }

    public String getPageLayout() {
      return "Current layout is " + page.getLayout();
    }
  }
```


``` java
  public class Reader {
    public static void main(String[] args) {
      Book book = new Book();

      System.out.println(book.getPageLayout());
    }
  }
```

위의 코드를 가지고 의존성 주입에 대해 살펴보자.
기존의 코드는 주석처리했다.


### 스프링 없이 의존성 주입 1 - 생성자
 
``` java
  public class Book {
    Page page;

    /*
    public Book() {
      this.page = new ContentsPage();
    }
    */
    public Book(Page page) {
      this.page = page;
    }

    public String getPageLayout() {
      return "Current layout is " + page.getLayout();
    }
  }
```

`Book` 의 생성자가 이제 `Page` 객체를 받는다.
이제 `Book` 객체는 항상 `Page` 객체를 **외부에서** 받아야 한다.


``` java
  public class Reader {
    public static void main(String[] args) {
      Page page = new ContentsPage();
      
      //Book book = new Book();
      Book book = new Book(page);

      System.out.println(book.getPageLayout());
    }
  }
```

`Book` 생성자의 변경에 따른 `Reader` 클라이언트 코드의 변경이다.
`Book` 의 외부는 `Reader` 이다.


### 스프링 없이 의존성 주입 2 - 속성을 통한 의존성 주입

``` java
  public class Book {
    Page page;

    /*
    public Book(Page page) {
      this.page = page;
    }
    */

    public Page getPage() {
      return this.page;
    }

    public Page setPage(Page page) {
      this.page = page;
    }

    public String getPageLayout() {
      return "Current layout is " + page.getLayout();
    }
  }
```

``` java
  public class Reader {
    public static void main(String[] args) {
      Page page = new ContentsPage();
      
      //Book book = new Book(page);
      Book book = new Book();
      book.setPage(page);

      System.out.println(book.getPageLayout());
    }
  }
```

이제 생성자를 이용하여 `Page` 객체를 주입하지 않고, 새로운 `setPage()` 메서드를 통해서 `Page` 객체를 주입할 수 있다.
이렇게 하면 여러 장점이 생기는 데, 다음과 같다.

1. 이미 생성해 두었던 `Book` 객체의 상태를 변경할 수 있다.
기존에는 다른 `Page`를 가진 `Book` 객체를 만들기 위해서는, 항상 새로운 `Book` 객체를 생성해야 했다.

2. `Book` 객체를 새로 생성할 때 `Page` 객체에 대해 몰라도 된다. 즉, 나중에 주입해도 상관 없다.
기존에는 `Page` 객체가 없으면 `Book` 객체를 생성하지 못했다.

3. **`Book` 객체를 생성하는 책임과, `Book` 객체에 `Page` 객체를 주입하는 책임의 대상이 분리된다.**
기존에는 `Book` 객체를 생성하는 쪽이 반드시 `Page` 객체도 주입할 책임을 가지고 있었으나, `setPage()`의 도입으로 책임이 분리되었다.

따라서 새로운 `Publisher` 클래스는 `Book` 객체에 `Page` 객체를 주입할 책임을 지고 있도록 설계한다고 가정해보자. 그렇다면 `Reader`는 단순히 `Publisher`가 생성한 `Book`의 객체를 가져오기만 하면 그 `Book`의 객체 안에는 `Page` 객체도 들어있을 것이다.  

그러나 아직 한계는 존재한다.
`Book`, `Page`, `Publisher` 모두 **코드**이며, 동시에 컴파일의 대상이기도 하다.
한 발 물러서 바라보면 결국 코드가 서로 다른 코드끼리 상호 의존하고 있으며, `Publisher` 같은 중간 클래스를 수 없이 도입한다고 해도 `Book`의 속성에 대해 변경 사항이 발생할 때마다 새로 컴파일해야 한다.

그렇다면 **코드 외부에 의존할 방법은 없을까?**


### 스프링을 통한 의존성 주입 1 - XML 사용

//TODO 마무리 작성
