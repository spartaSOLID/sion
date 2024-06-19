> 김영한 님의 JPA 기본편을 정리한 내용입니다.

tip
`JDBC`:  데이터를 데이터베이스에 저장 및 업데이트하거나, 데이터베이스에 저장된 데이터를 Java에서 사용할 수 있도록 하는 자바 API이다. 
(SQL 쿼리문 같은거)

### JPA란
- Java Persistence API
- 자바 진영의 `ORM` 기술 표준이다.

### 그럼 ORM기술은 뭔가?
- Object-relational mapping(객체 관계 매핑)
    - (객체랑 ↔ 관계형 데이터베이스를 매핑을 해준다는뜻) (중계자)
- 객체는 객체대로 설계하고, RDB는 RDB대로 설계한다. → ORM 프레임워크가 중간에서 연결, 매핑해준다
- <span style="color:indianred">JPA가 →  JDBC API를 대신 만들어줌 (JPA가 SQL문을 대신 만들어줌)</span>
- 예전엔 애플리케이션과 DB가 통신하려면 직접 → JDBC API를 만들어줬다.
----


## JPA는 애플리케이션과 JDBC 사이에서 동작한다
- JPA는 JDBC API를 사용하여 데이터베이스와  데이터를 주고받게 된다
![](https://velog.velcdn.com/images/siontext/post/8b69b2ea-0c35-4793-b9b4-cbf24be1b9c0/image.png)


## JPA 동작 - 저장
- MemberDAO 클래스를 통해 `persist()`를 실행하면,
→ JPA가 Entity 객체를 분석하여 SQL문을 생성한다.
- JDBC API를 사용하여 DB에 INSERT SQL(쿼리문)을 보내게 된다.
- 또한 JPA는 객체와 데이터베이스 테이블의 패러다임 불일치를 해결한다.
![](https://velog.velcdn.com/images/siontext/post/be3810a5-748c-43f3-941c-e216d6566a3d/image.png)



## JPA 동작 - 조회
- MemberDAO 클래스를 통해 `find(id)`를 실행하면 → JPA는 SELECT SQL을 생성한다.
- .JDBC API를 사용하여 생성된 SELECT SQL을 → DB로 보낸다.
- DB에서 반환된 정보를 ResultSet 매핑을 통해 객체로 변환해 준다
- 엔티티 객체로 만들어서 → 우리에게 반환해준다.
- 패러다임 불일치 문제를 해결해 준다.
![](https://velog.velcdn.com/images/siontext/post/eb182fc7-21e8-49cf-afa9-8057e3971a9c/image.png)




### JPA는 표준명세 이다.
- JPA는 인터페이스이고
- JPA를 구현한 → 3가지 구현체가 있음
- 거의 대부분 하이버네이트를 구현체로 쓴다 
(나중에 설정파일에 들어있는 하이버 네이트를 보고 → 아 얘가 JPA를 사용할 수 있도록 구현해주는 라이브러리 겠구나 하면됨)
![](https://velog.velcdn.com/images/siontext/post/18d1ab06-77b0-46d5-b85b-bed0cce762bb/image.png)

-----------

### JPA를 사용하는 이유
- SQL 중심적인 개발에서 → 객체 중심으로 개발이 가능
    - 조회할떄 → 이 멤버객체 찾아줘 (JPA 에게 객체를 던지면됨)
    

#### 1. 생산성이 올라간다
- SQL 쿼리문을 직접 작성할 필요 없이 DB에 접근할 수 있고
- 간단한 메서드를 통해 CRUD가 가능→ 생산성이 올라간다.
```
	저장: jpa.persist(member) 
	조회: Member member = jpa.find(memberId)
	수정: member.setName(“변경할 이름”)
	삭제: jpa.remove(member)
```

#### 2. 유지보수 편하다.
- 기존엔 필드를 변경할 시 모든 SQL을 수정해야했음
- JPA 사용시 → jpa수정 메서드 딸깍 → JPA가 SQL문 쿼리문 알아서 작성

#### 3. 패러다임과 불일치 문제 해결
- 상속, 연관관계, 객체 그래프 탐색, 비교 등의 설계 차이로 인해 발생하는 패러다임 불일치 문제를 해결해준다.
    - DB테이블은 상속개념 존재 X
    - 각체는 참조를 통해 관계를 표현, DB는 외래키를 통해 관계를 표현
    - 이런 객체와 DB의 불일치 문제를 → JPA형님이 해결해 주신다.

#### 4. 성능 최적화 기능도 해준다.
1. 1차 캐시와 동일성(identity) 보장
2. 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
3. 지연 로딩(Lazy Loading)

><span style="color:slateblue">1차캐시와 동일성 보장
>- 같은 트랜잭션 안에서는 같은 엔티티를 반환 - 약간의 조회 성능 향상
>- 1차캐시에 저장되있는걸 → 반환해주는듯
>![](https://velog.velcdn.com/images/siontext/post/e5c538e9-2689-4f50-8aac-d2f301224bf1/image.png)

  ><span style="color:slateblue">트랜잭션을 지원하는 쓰기 지연 - INSERT 
>- UPDATE, DELETE로 인한 로우(ROW)락 시간 최소화
>- 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고, 바로 커밋
>  ![](https://velog.velcdn.com/images/siontext/post/223e20e9-7178-47e8-88b1-d0a4edf36b8e/image.png)
><span style="color:slateblue">트랜잭션을 지원하는 쓰기 지연 - UPDATE (나중에 설명한다고 넘어감)
>- UPDATE, DELETE로 인한 로우(ROW)락 시간 최소화
>- 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고, 바로 커밋![](https://velog.velcdn.com/images/siontext/post/ba244b91-16ae-48d3-8027-d2eb465c01aa/image.png)

  ><span style="color:slateblue">지연 로딩과 즉시 로딩 기능을 제공
>- 지연로딩: 객체가 실제 사용될 때 로딩
>- 즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회
>- 예시) (멤버랑 팀이 연관관계라 → 멤버를 조회할떄 항상 팀이 같이 조회되네) (멤버만 조회하고 싶다)
  ```java
지연로딩에선 
memberDAO.find(memberId); => 멤버만 조회함
member.getTeam(); => 해야 팀도 조회가됨 (이것이 지연로딩)
team.getName(); => 해야 팀의 이름이 조회된다.
  ```
  ```
그러나 즉시로딩은
memberDAO.find(memberId); 
=> 하면 멤버 + 팀 다 조회됨 (memberId 관련된거 다끌고온다)
만약 (난 멤버를 조회할때 무조건 팀 정보도 필요하다 => 즉시로딩으로 설정하고 쓰면됨)
```
  
  ![](https://velog.velcdn.com/images/siontext/post/767f5106-0d29-4b7a-a039-836a23c405f2/image.png)


