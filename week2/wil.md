# week2

## Spring Bean
1) Spring: JAVA의 대표적인 backend framework
2) Spring Boot: Spring framework를 사용하여 개발할 때, 편의를 더해주는 도구
3) Spring Application 구조
- 1. 요청 처리 명령
- 2. 내장 tomcat server에서 전용 컨트롤러로 해당 요청 처리를 보내줌
- 3. 전용 컨트롤러에서 DB에 접속해서 필요한 정보 가져오기
- 4. 전용 컨트롤러가 반환한 것은 JAVA 객체로 받아옴
- 5. front에 전달하기 위해 json 컨버터에서 json형식으로 바꾸기
- 6. tomcat server에서 다시 바꾼 내용을 http에 담아서 응답으로 내보내주게 됨

* Spring Bean
  : 어플리케이션 전역에서 사용할 공용 자바 객체
  - Spring Container라고 하는 공용창고에 빈 객체를 저장해두고, 필요한 빈을 컨테이너에서 받아 사용
  - 컨테이너 안에서 이미 생성된 객체를 가지고 재사용하기
  - 필요한 bean은 Spring framework 가 자동으로 가져다 줌/ bean을 요구하는 객체도 Spring Bean
  - 단, 빈이 아닌 일반 객체일 땐, framework가 자동으로 갖다주진 않음!

* Spring Container
  : Spring Bean이 저장되는 공간
  - JAVA code로 생성 시에 'Application Context'라고 치기

## Spring Bean 저장 방법
1. 설정 파일 작성 - 수동 등록
- 내가 등록하고 싶은 객체를 하나하나 나열한 다음에 Spring에 건네줌
- Application이 실행될 때, 설정 파일을 쭉 훑으면서 등록해줌
- 명시적으로 어떤 파일을 등록할 것인지 직접 작성
    1) 설정 파일은 JAVA Class로 작성
    2) 클래스에 @Configuration 으로 설정 파일임을 명시
    * Application Context로 Spring container를 만들고 나서, 이 안에 있는 myBean은 유일한 하나의 객체이기 때문에
      Spring container에서 객체를 요청할 때마다 myBean 객체는 언제나 고유값도 똑같은 같은 객체임

2. 컴포넌트 스캔 - 자동 등록
- Spring Bean도 객체이기 때문에 Class 필요
- 클래스로부터 객체가 나오기 때문
- 클래스에다가 "이 클래스가 생성하는 객체는 스프링 빈으로 등록해줘"라고 표시
- Application이 실행될 때, 클래스의 표식을 보고 등록해주기
    1) 빈을 생성할 클래스에 @Component 사용
    2) Application 시작할 때 @Component가 붙은 클래스를 찾아서 자동으로 객체를 생성 후 등록
    3) 설정 파일로 썼던 내용 대신에 @ComponentScan을 이용하기

3. 정리
1) 설정 파일 작성: use @Configuration, @Bean
2) 컴포넌트 스캔: use @Component, @ComponentScan

## Spring Bean 받아오는 방법

### 의존성 주입
- Container에 직접 접근해서 빈을 꺼내오는 것이 아니라
- framework에게 필요한 빈을 요청하고 받아서 사용
- A의 기능을 실행하는데 B의 기능이 필요하다: 'A는 B에 의존한다'
- 즉 내가 의존하는 객체를 직접 생성하지 않고 (new()로 새로 받을 필요 없음) 밖에서 주입받는 것
- * 빈이 아닌 객체에 빈을 자동으로 주입은 불가
  - Spring bean으로 등록은 공동 창고를 의미하기도 하지만, 내가 이 객체를 관리하는 것을 framework에게 맡기겠다는 뜻!
- 생성해둔 객체를 사용하므로 memory를 효율적으로 사용 가능
- bean package에 MySubBean 클래스를 생성 후 빈으로 등록 - @Component이용
- MyBean에 MySubBean 의존성과 @Getter 추가 - 가져오는 방법 만들어 준 것

### 의존성 주입 방법
1. 어떤 객체를 다른 객체에 주입하려면 통로 필요 : 생성자/ 필드 / 메서드
2. 통로를 통해 주입해 달라고 표시해야함: @Autowired 이용하기
   
1) 생성자 주입: 동작하는 Application code 쓸 때 이용
   1. 기본 방법
   - 의존성이 바뀔 일이 없을 경우 안전하게 final로 선언
   - final field는 생성자를 통해 초기화 필요
   - 생성자 + @Autowired -> 생성자를 통해 빈을 주입
   - - 단) 생성자가 하나만 있다면 @Autowired 생략 가능

   2. 간단한 버전 (Lombok이 제공하는 기능)
   - @RequiredArgsConstructor 사용
   - 모든 final field에 대한 생성자를 자동으로 만들어줘서 생성자 code까지 생략 가능
   - 1번으로는 반드시 field를 초기화해주는 생성자 code가 필요했는데, 2번으로는 한번에 해결 가능

  * 1. 필요한 의존성을 final keyword사용해 추가하기
  * 2. @RequiredArgsConstructor를 사용해 생성자를 추가하기

2) 필드 주입: 주로 test code 작성할 때 이용
   - field에 바로 @Autowired 이용 ( final 은 사용 불가 ) 
   - 단 이전까지는 beantest할 때, container가 없는 상태에서 직접 container를 만들어서 testgka
   - BUT ! test가 실행되기 전에 미리 container가 필요하고 
   - bean이 들어가 있어야하며
   - test를 실행해야함
   - 클래스에 @SpringBootTest하면 Application에 있는 모든 bean을 container에 등록한 후 test함
   - => SpringBoot까지 실행시키므로, 통합 test에도 사용할 수 있음

## Spring Bean 의 활용
### Spring Bean Layered Architecture

1. Controller
   - Client 요청 받고, 응답 보내기
   - DTO(Data Transfer Object) 사용하여 service 계층과 data 주고받기

2. Service
    - Application 의 business logic이 담김: 진짜 application이 해야할 일을 처리해주는 것
    - Repository layer와 소통, entity/DTO로 소통

3. Repository
   - DB와 소통하며 data 조작
   - 2번의 business logic을 실제 DB에 적용

4. DB( Data Base)

* 1,2,3번 과정은 Spring Bean으로 등록해서 이용함
