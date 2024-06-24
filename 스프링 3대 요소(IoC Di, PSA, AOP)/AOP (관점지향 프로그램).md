### 주요 용어

| 용어 | 설명 |
| --- | --- |
| Aspect | - 공통적인 기능들을 모듈화 한것을 의미합니다.  |
| Target | - Aspect가 적용될 대상을 의미하며 메소드, 클래스 등이 이에 해당 됩니다. |
| Join point | - Aspect가 적용될 수 있는 시점을 의미하며 메소드 실행 전, 후 등이 될 수 있습니다. |
| Advice | - Aspect의 기능을 정의한 것으로 메서드의 실행 전, 후, 예외 처리 발생 시 실행되는 코드를 의미합니다. |
| Point cut | - Advice를 적용할 메소드의 범위를 지정하는 것을 의미합니다. |

### 주요 어노테이션 (AOP입니다 + 부가 기능을 언제 실행할 것인가)

| @Aspect | 해당 클래스를 Aspect로 사용하겠다는 것을 명시합니다. (이 클래스는 AOP입니다.) |
| --- | --- |
| @Before |  '핵심기능' 이 실행되기 전에 Advice를 실행합니다. |
| @AfterReturning |  '핵심기능' 이 정상적으로 실행되고 반환된 후에 Advice를 실행합니다. |
| @AfterThrowing |  '핵심기능' 에서 예외가 발생”했을 때 Advice를 실행합니다. |
| @After |  '핵심기능' 이 실행된 후에 Advice를 실행합니다. |
| @Around |  '핵심기능'  실행 전, 후 또는 예외 발생 시에 Advice를 실행합니다. |

----

### AOP란?

- 로직들을 (**공통 관심사항**, **핵심  관심사항**)으로 나누어서 프로그래밍 하는것
- 그리고 이것으로 AOP를 구현할 수 있다.

### AOP가 필요한 상황

>상황극
- 사수가 와서 모든 메소드의 호출 시간을 측정하고 측정하라고 한다.
- 그래서 모든 로직 (컨트롤러, 서비스, 리포지토리에) 의 시작과 끝에 시작측정로직을 다 집어넣었다.
- 사수왈: “야 초단위로 찍으니깐  잘 안나와 → 밀리세컨드로 바꿔줘” → 또 로직 다바꿔??
```java
@Transactional
public class MemberService {
	// 회원가입 메서드
	public Long join(Member member) {
		long start = System.currentTimeMillis(); //시작 시간 변수
	try {
		validateDuplicateMember(member); //중복 회원 검증 (핵심관심사항)
		memberRepository.save(member); //회원 저장
		return member.getId();
	} finally {
		long finish = System.currentTimeMillis(); //끝 나는 시간 변수 
		long timeMs = finish - start; //로직의 시간 재기 (공통 관심 사항)
		System.out.println("join " + timeMs + "ms");
}
}
```
![](https://velog.velcdn.com/images/siontext/post/c7835af1-dd14-48b0-a29e-0d277ad2d23d/image.png)

위 상황에선 이러한 문제들이 생기게 된다. 
- 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
- 시간을 측정하는 로직은 공통 관심 사항이다.
- 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
- 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.

 이것을  AOP로 해결할수 있다.
 
 -----
 ### AOP 적용

- AOP(Aspect Oriented Programming)
- 공통관심사항 vs 핵심 관심사항으로 분리
![](https://velog.velcdn.com/images/siontext/post/dfe787d1-89ad-4862-a228-6ff21952b993/image.png)
---
### 공통 모듈 분석

- 시간 측정로직은 공통관심 사항으로 → AOP로 구현할 수 있다.

### 시간 측정 AOP 등록 (공통묘듈 추출 및 구현)

```java
@Component //스프링 빈으로 등록
@Aspect //AOP 설정을 할 클래스이다 설정
public class TimeTraceAop {
	 
	 @Around("execution(* hello.hellospring..*(..))") //execution: 적용 범위
	 public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
			 long start = System.currentTimeMillis();
			 System.out.println("START: " + joinPoint.toString());//시작시간
	 try {
			 return joinPoint.proceed();
 } finally {
			 long finish = System.currentTimeMillis(); //끝나는 시간
			 long timeMs = finish - start; // 메서드의 작동시간 측정
			 System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
 }
 }
}
```
- 시간 측정 AOP의 적용범위를 (* hello.hellospring..*(..))") hello.hellospring패키지 하위 전체로 설정해 놨으니
- 해당되는 하위 클래스, 패키지들은 이제 작동할때마다 로그에 메서드 동작 시간이 찍히게 된다.


### 해결

- **회원가입, 회원 조회**등 <u/>핵심 관심사항</u> VS **시간을 측정**하는 <u/>공통 관심 사항</u>을 분리한다
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다 → 공통 관심사항을 분리
- 장점
    - 핵심 관심 사항을 깔끔하게 유지할 수 있다.
    - 변경이 필요하면 이 로직만 변경하면 된다
    - 원하는 적용 대상을 선택할 수 있다.

---
### 스프링 AOP 동작방식 설명

1. AOP적용 전 의존관계
![](https://velog.velcdn.com/images/siontext/post/572692ab-0100-4c72-adda-f1de6396e61c/image.png)
- `memberController`는 `memberService`의 기능들을 사용하며 의존관계가 수립됩니다.
- 컨트롤러에서는 memberService의 실제 객체(Real Subject)에 바로 접근하여 로직을 수행시킵니다.
- `memberService` 에서는 공통적인 기능들을 추가하려면 각각의 메서드마다 기능들을 추가/수정/삭제 해야합니다.


2. AOP 적용 후 의존관계
![](https://velog.velcdn.com/images/siontext/post/b5dec9a6-bfdc-40ba-a362-18bcf84b05bb/image.png)
- `memberController` 는 이제  실제객체(Real Subject)에 바로 접근하지않고 프록시객체를 거쳐 프록시 객체에서 실제 객체에 접근하여 로직을 수행합니다.

3. AOP 적용 전 전체 그림
![](https://velog.velcdn.com/images/siontext/post/18fb5ed8-74c8-4265-832e-3eaba8efe81e/image.png)
- 모든 객체들에게 프록시객체가 생성되었고 의존관계도 프록시 객체를 통하여 이뤄진다
- 각각 객체의 기능들을 수행하려 메서드를 호출하면 요청을 프록시 객체가 전달받아서 전처리/후처리 등 추가적인 작업을 수행하고 → 실제 객체(Real Subject)에 로직을(메서드) 수행

질문: AOP 를 적용하면 → 처음에 프록시객체를 전달받아서 전처리 과정 (예: 메서드 시간계산)을 하고 → 실제 객체의 메서드를 호출해 로직을 수행하는건가? (일단 GPT는 맞다고함)
