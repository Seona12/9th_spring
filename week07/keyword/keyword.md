# 🎯 핵심 키워드
## RestContollerAdvice
1. 장점
- RestControllerAdvice은 무분별한 try-catch가 없어 가독성에 좋다.
- 하나의 클래스로 모든 Controller에 대한 전역적인 예외처리가 가능해서 훨씬 깔끔하게 예외처리 가능
- 예외에 따라 다른 처리 로직 적용 가능
    - `@ExceptionHandler` 어노테이션을 사용하여 특정 예외에 대한 `핸들러 메서드`를 정의할 수 있다.

      ex) NullPointerException이 발생하면 400 에러 코드와 에러 메시지를 반환하고, IOException이 발생하면 500 에러 코드와 에러 메시지를 반환하는 등의 로직을 구현할 수 있습니다.

2. 없을 경우 불편한 점
     - 컨트롤러마다 중복된 try-catch 필요
         - 각 메서드마다 동일한 예외 처리 로직을 직접 작성해야하므로 중복 코드가 많음
     - 예외 처리 로직 분산으로 유지보수 어려움
         - 예외 처리 코드가 여러 곳에 흩어져 있어 수정할 때마다 모든 컨트롤러를 일일이 찾아가야 한다.
     - 특정 예외 누락 위험
         - 의도치 않게 예외 타입을 빠뜨리면 애플리케이션이 비정상 종료되거나 스택트레이스가 그대로 노출될 수 있다.

   [https://velog.io/@woosim34/RestControllerAdvice를-이용한-예외처리](https://velog.io/@woosim34/RestControllerAdvice%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC)

## lombok

  LomBok : **어노테이션 기반으로 코드 자동완성 기능을 제공하는 라이브러리**

  ⇒ Web 개발을 하다보면 등장하는 반**복되는 코드를 줄여준다.**

  ### 장점

1. 어노테이션을 통한 코드 자동 생성을 통한 생산성, 편의성 증가
2. Code의 길이가 줄어듬으로 가독성, 유지보수성 향상
3. Builder 패턴의 적용, Log 생성 등등 편의성

  ### 종류

- @Data
    - @Getter, @Setter, @ToString, @RequiredArgsConstructor, @EqualsAndHashCode 어노테이션의 집합체
    - 단점 1 : @ToString이 포함되어있으므로, 양방향 연관관계에서 순환참조 문제가 발생
    - 단점 2 : setter 가 포함되어 어디서든 변경 가능
- **@Getter,  @Setter**
    - **클래스 위에 적용시킨다면 모든 변수**들에 적용
    - 필드(변수) 위에만 할당하면 **해당 변수에만 적용 가능**
- **@ToString**
    - 클래스 전체에 적용시킨다면 해당 클래스 변수들을 toString 메서드를 자동 완성
- **@AllArgsConstructor**
    - 모든 필드를 사용하는 생성자를 만들어 주는 어노테이션
- **@NoArgsConstructor**
    - 파라미터가 없는 생성자 만들어줌
- **@RequiredArgsConstructor**
    - final 키워드가 붙은 필드를 포함하여 생성자를 생성
    - DI 주입시 주로 사용
- **@Builder**
    - 클래스 위에 적용 한다면 해당 클래스 모든 변수에 대해서 빌더 패턴을 사용할 수 있게 해준다.
- https://lucas-owner.tistory.com/27

# dto 형식 public static VS record 비교하기

| 구분 | `public static class` DTO | `record` DTO |
| --- | --- | --- |
| **도입 시기** | Java 초창기부터 존재 | Java 14 (Preview) → Java 16부터 정식 |
| **목적** | 값 전달용 객체를 명시적으로 정의 | 불변(immutable)한 데이터 보관용 |
| **코드량** | Getter/Setter, 생성자, equals, hashCode, toString 직접 작성 필요 (Lombok으로 단축 가능) | 자동 생성됨 (압도적으로 간결) |
| **불변성 (Immutability)** | 기본적으로 **가변(mutable)** — `setter`로 수정 가능 | 기본적으로 **불변(immutable)** — 필드 수정 불가 |
| **계층 구조** | 다른 클래스 내부에 `public static class`로 작성 가능 (ex: `Response` 안의 `Dto`) | 반드시 **독립적인 top-level 클래스**여야 함 |
| **상속 가능 여부** | 일반 클래스처럼 상속 가능 (`extends`, `implements`) | **상속 불가**, 단 **interface 구현은 가능** |
| **직렬화 (Serialization)** | 별도 `implements Serializable` 필요 | 자동으로 가능 (`Serializable` 인터페이스 구현 가능) |
| **Lombok 사용 여부** | 보통 `@Getter`, `@Setter`, `@NoArgsConstructor`, `@AllArgsConstructor` 등 필요 | Lombok 불필요 |
| **가독성 / 유지보수성** | 구조 복잡한 경우 유리 (커스텀 메서드, 로직 포함 가능) | 데이터 전달용으로 깔끔하고 명확 |
| **Spring 호환성** | Jackson이 기본적으로 setter 필요 → `@JsonProperty`, `@JsonCreator` 등 설정 필요 | Spring 6 / Jackson 2.12+에서는 자동으로 잘 작동 |
| **대표 사용 예시** | 요청(Request) DTO, 엔티티 변환용 DTO | 응답(Response) DTO, 단순 데이터 반환 객체 |