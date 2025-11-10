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

> **github 링크**
> 
> 시니어 미션까지 함께 있습니다!
> https://github.com/Seona12/Feat-Chapter4.git
