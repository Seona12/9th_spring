# 🎯핵심 키워드

---
# java의 Exception 종류들
Java의 에러 계층 구조는 크게 3개로 나뉜다:

```
Throwable
 ├─ Error (Unchecked)
 └─ Exception
       ├─ Checked Exception
       └─ Unchecked Exception (RuntimeException)

```

---

# 1️⃣ **Error (Unchecked)**

시스템 레벨 문제 — 개발자가 처리할 수 없음.

**catch 하지 않는 것이 일반적.**

| 주요 종류 | 설명 |
| --- | --- |
| `OutOfMemoryError` | 힙 메모리 부족 |
| `StackOverflowError` | 재귀 호출 무한루프로 스택 메모리 초과 |
| `NoClassDefFoundError` | 컴파일 시 존재했는데 실행 시 클래스 로딩 실패 |
| `AssertionError` | assert 실패 |

---

# 2️⃣ **Exception → Checked Exception**

**컴파일러가 처리 강제** (try-catch 또는 throws 필요)

대표적: IO, DB, 네트워크 관련

| 주요 종류 | 설명 |
| --- | --- |
| `IOException` | 파일/스트림 입출력 오류 |
| `FileNotFoundException` | 파일이 없음 |
| `SQLException` | DB 접근 오류 (JDBC) |
| `ClassNotFoundException` | 클래스 로딩 실패 |
| `CloneNotSupportedException` | clone() 지원하지 않는 객체 복제 시 |
| `InterruptedException` | 스레드 인터럽트 |
| `TimeoutException` | 타임아웃 발생 |
| `MalformedURLException` | URL 형식 잘못됨 |

---

# 3️⃣ **Exception → Unchecked Exception (RuntimeException)**

**컴파일러가 강제하지 않음**

Null, Index 등 프로그래밍 오류 대부분

| 주요 종류 | 설명 |
| --- | --- |
| `NullPointerException` | NPE — null 접근 |
| `IndexOutOfBoundsException` | 배열/리스트 범위 초과 |
| `ArithmeticException` | 0으로 나누기 등 수학 오류 |
| `IllegalArgumentException` | 잘못된 인자 전달 |
| `IllegalStateException` | 객체 상태가 잘못된 경우 |
| `NumberFormatException` | 문자열 → 숫자 변환 실패 |
| `ClassCastException` | 잘못된 타입 캐스팅 |
| `UnsupportedOperationException` | 지원하지 않는 기능 호출 |
| `ConcurrentModificationException` | 컬렉션 동시 수정 시 발생 |

# @Valid

  `@Valid`는 **Java Bean Validation**을 적용해 DTO의 필드값을 자동 검증(Validation)해주는 기능이다.

  주로

    - Controller의 요청 DTO 검증
    - Service/Method 파라미터 검증
    - Entity 검증

      할 때 사용됨.