# 지연로딩과 즉시로딩의 차이

  ### **JPA에서의 즉시로딩과 지연로딩의 개념**

    - **즉시로딩 (xxToxx(fetch = fetchType.EAGER): 데이터를 조회할 때, 연관된 모든 객체의 데이터까지 한 번에 불러오는 것이다.**
        - join sql로 한번에 연관된 객체까지 미리 조회
        - 실무에서 가장 사용하지 않아야 할 로딩방식
    - **지연로딩 : @xxToxx(fetch = fetchType.LAZY)** :  필요한 시점에 연관된 객체의 데이터를 불러오는 것이다.

  ⇒ 즉시 로딩에서는 member와 연관된 Team이 1개여서 Team을 조회하는 쿼리가 나갔지만, 만약 Member를 조회하는 JPQL을 날렸는데 연관된 Team이 1000개라면? Member를 조회하는 쿼리를 하나 날렸을 뿐인데 Team을 조회하는 SQL 쿼리 1000개가 추가로 나가게 된다.

  그러므로, 지연로딩을 사용하는 것이 좋다.

  ### 기대했던 sql문

    ```java
    SELECT * FROM user;
    ```

  ### JPA가 실행한 sql문

    ```java
    1. SELECT * FROM user;   (모든 유저 조회) ➔ 1번
    2. SELECT * FROM article WHERE user_id = 1; ➔ 1번
    3. SELECT * FROM article WHERE user_id = 2; ➔ 1번
    4. SELECT * FROM article WHERE user_id = 3; ➔ 1번
    ...
    ```
# JPQL
    - (Java Persistence Query Language) 객체지향 쿼리로 **JPA가 지원하는 다양한 쿼리 방법 중 하나**
    - SQL과의 차이점
        - SQL : 테이블을 대상으로 쿼리
        - JPQL : 엔티티 객체를 대상으로 쿼리(테이블 대신 클래스 이름 사용)
            - 쿼리 결과 : 객체(entity)
            - 실제 사용 예시 :

            ```java
            // 예: 나이가 20보다 큰 회원 조회
            String jpql = "SELECT m FROM Member m WHERE m.age > 20";
            
            //쿼리 결과로 나오는 Member 엔티티들의 집합을 저장
            List<Member> result = em.createQuery(jpql, Member.class)
                                    .getResultList();
            ```

# Fetch Join

  JPQL에서 성능 최적화를 위해 제공하는 조인의 종류.

  ### 해결 방법

    - jpql에서 fetch join 사용!
        - 하드코딩을 막기 위해서는 `@EntityGraph`를 사용하면 된다.

        ```java
        @EntityGraph(attributePaths = {"articles"}, type = EntityGraphType.FETCH)
        List<User> findAllEntityGraph();
        ```

# @EntityGraph

  ## @EntityGraph

  Spring Data JPA에서는 **`@EntityGraph`**를 사용해서 `메서드 이름으로 쿼리 생성 + fetch join` 해결

  **`@EntityGraph(attributePaths = {"fetch join 할 객체의 필드명"}`**

  ⇒ fetch join이 가능하다.

  ### 📁 MemberRepository

    ```java
        // Entity Graph
        @Override
        @EntityGraph(attributePaths = {"team"}) // 객체의 필드명
        List<Member> findAll();
    
        // JPQL에 Entity Graph추가(fetch join)
        @EntityGraph(attributePaths = {"team"})
        @Query("select m from Member m")
        List<Member> findMemberEntityGraph();
    
        // 메서드 이름으로 쿼리 생성 + EntityGraph추가(fetch join)
         @EntityGraph(attributePaths = {"team"})
        List<Member> findEntityGraphByUsername(@Param("username") String username);
    ```
# commit과 flush 차이점

| 구분           | flush                                   | commit                           |
| ------------ | --------------------------------------- | -------------------------------- |
| **역할**       | 영속성 컨텍스트의 변경 내용을 **DB에 반영(쿼리 실행)**      | 트랜잭션을 **완전히 종료하고 확정**            |
| **시점**       | `em.flush()` 호출 시 or `commit` 직전 자동 호출  | `em.getTransaction().commit()` 시 |
| **DB 반영 여부** | SQL 실행은 되지만 트랜잭션이 아직 **확정 안 됨 (롤백 가능)** | **확정됨**, 롤백 불가                   |
| **주요 목적**    | 1차 캐시(영속성 컨텍스트)와 DB의 상태를 맞춤             | 트랜잭션 단위의 작업을 마무리                 |
## query DSL이란?

> 정적 타입을 이용해서 SQL, JPQL을 코드로 작성할 수 있도록 도와주는 오픈소스 빌더 API
>
- 어떻게 문자열 형태인 JPQL을 보완 했을가요?

  바로 쿼리에 대한 내용을 `함수 형태로 제공`하여 보완했습니다!


## 💡 QueryDSL을 사용하는 이유

**1. 자바 코드로 쿼리를 작성함으로 컴파일 시점에 에러를 잡을 수 있다.**

기존 JPQL은 쿼리를 문자열로 작성해야한다. 만약 오타가 있거나 잘못 작성한다해도 컴파일 시점에 에러가 발생하지 않고 런타임 시점에 발생하기 때문에 실행시키기 전에는 잘못된 부분을 알 수 없다. (**최악!**)

하지만 QueryDSL은 자바 코드로 쿼리를 작성하기 때문에 **컴파일 시점에 에러를 잡을 수 있다**는 큰 장점이 있다.

**2. 복잡한 동적 쿼리를 쉽게 다룰 수 있다.**

JPQL을 이용해 동적 쿼리를 다루기 위해서는 문자열을 조건에 맞게 조합해서 사용해야한다. 이는 코드도 복잡해지고 런타임 에러를 발생시키는 치명적인 단점이 있다.

QueryDSL은 복잡한 동적 쿼리도 Q클래스, 메서드를 활용하여 쉽게 다룰 수 있다.

---

# OpenFeign QueryDSL

- 공식 QueryDSL은 업데이트가 없고 SQL 인젝션 취약점 문제도 존재 ⇒ OpenFeign의 QueryDSL은 25년 7월 기준 7.0까지 나온상태고 계속 유지보수가 이루어지고 있으므로 **QueryDSL를 도입하는 추세**
- **build.gradle**

    ```java
    // QueryDSL 설정
    def queryDslVersion = "7.0" // 25년 7월 기준 가장 최신
    def querydslSrcDir  = layout.buildDirectory.dir("generated/querydsl").get().asFile // build/generated/querydsl
    
    tasks.withType(JavaCompile).configureEach {
        options.getGeneratedSourceOutputDirectory().set(file(querydslSrcDir))
    }
    
    sourceSets {
        main {
            java {
                srcDirs += querydslSrcDir
            }
        }
    }
    configurations {
        querydsl.extendsFrom compileClasspath
    }
    //
    
    dependencies {
    .. 중략
    
        //Querydsl 추가
        implementation("io.github.openfeign.querydsl:querydsl-core:${queryDslVersion}")
        implementation("io.github.openfeign.querydsl:querydsl-jpa:$queryDslVersion")
        annotationProcessor("io.github.openfeign.querydsl:querydsl-apt:$queryDslVersion:jpa")
    
    ..중략
    }
     
    
    Qclass 생성 경로는 build/generated/querydsl로 설정하였다.
    
     
    ```

- **setting/querydsl.gradle**

    ```java
    // querydsl.gradle
    def querydslSrcDir  = layout.buildDirectory.dir("generated/querydsl").get().asFile
    
    tasks.withType(JavaCompile).configureEach {
        options.getGeneratedSourceOutputDirectory().set(file(querydslSrcDir))
    }
    
    sourceSets {
        main {
            java {
                srcDirs += querydslSrcDir
            }
        }
    }
    configurations {
        querydsl.extendsFrom compileClasspath
    }
    
    ```

# N+1 문제 해결할 수 있는 여러 방안들
    1. **fetch join**
        1. JPA가 자동으로 먼저 생성해주는 Jpql을 통해서 우선적으로 쿼리를 만들다보니 연관관계가 걸려있어도 join이 바로 걸리지 않는다.
        2. ex)  쿼리를 날릴 때 article을 한번에 모두 가져옴

        ```java
        @Test
        @DisplayName("fetch join을 하면 N+1문제가 발생하지 않는다.")
        void fetchJoinTest() {
            System.out.println("== start ==");
            List<User> users = userRepository.findAllJPQLFetch();
            System.out.println("== find all ==");
            for (User user : users) {
                System.out.println(user.articles().size());
            }
        }
        ```

    2. **@EntityGraph**
        1. fetch join을 통해 바로 조회할 수 있음을 확인가능
           `//one tomany 있는걸 활용해서 해당 데이터 조회하기. 컨트롤러에서 조회. expert.getcareerimage`

        ```java
        @EntityGraph(attributePaths = {"articles"}, type = EntityGraphType.FETCH)
        @Query("select distinct u from User u left join u.articles")
        List<User> findAllEntityGraph();```

# 영속 상태의 종류

| 상태                 | 설명                              | 예시                                   |
| ------------------ | ------------------------------- | ------------------------------------ |
| **비영속 (new)**      | 영속성 컨텍스트에 아직 저장되지 않은 상태         | `Member member = new Member();`      |
| **영속 (managed)**   | JPA가 엔티티를 관리 중 (1차 캐시에 저장됨)     | `em.persist(member);`                |
| **준영속 (detached)** | 영속성 컨텍스트에서 분리된 상태 (더 이상 관리 안 됨) | `em.detach(member);` or `em.clear()` |
| **삭제 (removed)**   | 삭제 예약 상태 (commit 시 실제 DB에서 삭제)  | `em.remove(member);`                 |
