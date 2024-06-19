> 김영한 님의 Spring 기본편을 정리한 내용입니다.

### 스프링 빈(Bean) 이란?

- 스프링은 스프링컨테이너를 통해 객체를 관리한다고 한다.
- 그리고 스프링 컨테이너가 관리하는 객체를 `Bean` 이라고 한다.
- 사용이유 → 의존관계를 파악하기 위해서? → 얘 구현체는 누구야??!
- 스프링 빈을 생성하는 방법은 Component Scanning 방법과, 일일히 XML 이나 자바 설정파일에 등록하는 방법이 있다
- 컴포넌트 스캐닝 방법: 컴포넌트 애노테이션 달아두면 → 얘 스프링 빈이에요! 스캔을 해서 빈 등록해줌


### 스프링 컨테이너 생성

```java
//스프링 컨테이너 생성
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

- 일반적으로`ApplicationContext`를 스프링 컨테이너 라고 부른다.
- `ApplicationContext` 는 인터페이스이다.  그럼`new AnnotationConfigApplicationContext(AppConfig.class);` 는 인터페이스 구현체겠죠?
- 스프링 컨테이너를 생성할때 구성정보를 지정해줘야한다. → 따라서 매개변수로 AppConfig.class를 구성정보로 넣어줬다.

### 스프링 컨테이너의 생성 과정

1. **스프링 컨테이너 생성**
- 스프링 컨테이너 안에는→ 스프링 빈 저장소가 존재
- 스프링 컨테이너를 생성할 때 구성정보를 지정해줘야한다. → 그래서 `AppConfig.class`를 매개변수로 넣어 구성정보로 설정했다.
![](https://velog.velcdn.com/images/siontext/post/9ad12a3b-bbb6-4df5-bf95-f8f5066fe8bc/image.png)


2.  **스프링 빈 등록**
- 스프링 컨테이너는 구성정보를 보고 → `@Bean`  설정된 객체들을 스프링 빈으로 등록해줌
- `key` = 빈 이름 / `value` = 빈 객체임
- 빈 이름은 → 메서드 이름을 그대로 사용할수도 있고 직접 부여할수도 있다.
(예시: `@Bean(name="memberService2")`)
![](https://velog.velcdn.com/images/siontext/post/52813ea9-8c71-47c4-b051-434dbfce3f65/image.png)

3.  **스프링 빈 의존관계 설정** 
- 스프링 컨테이너는 → 설정 정보를 참고해 의존관계를(DI) 주입한다. (`AppConfig.class`)
- 예시)
    - 멤버 서비스 스프링빈에(인터페이스, 역할) → 멤버서비스구현체 = 메모리 멤버 리파지토리
    - 멤버 리파지토리 스프링빈(역할)에 → 메모리 멤버 리파지토리 구현체
    ![](https://velog.velcdn.com/images/siontext/post/ce18401e-94e5-4bb8-a64e-c2401dd00279/image.png)
