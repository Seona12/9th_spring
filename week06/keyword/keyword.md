# 1. QueryDSL에서 FetchJoin 하는 법

### 개념
Fetch Join은 JPA의 N+1 문제를 해결하기 위해 연관된 엔티티를 **즉시 로딩(EAGER)** 하면서도 성능 손실을 막기 위한 방법이다.  
QueryDSL에서는 `join(...).fetchJoin()` 으로 사용할 수 있다.

### 예시
```java
QMember member = QMember.member;
QTeam team = QTeam.team;

List<Member> result = queryFactory
    .selectFrom(member)
    .join(member.team, team).fetchJoin()
    .where(member.username.eq("선아"))
    .fetch();
 ```
### 주의사항
fetchJoin()은 select 절이 엔티티일 때만 의미 있음

DTO projection 시에는 효과 없음

Pageable과 함께 사용 시 페이징 불가 (메모리 페이징 필요)

# 2. DTO 매핑 방식 (+DTO 안에 DTO)
### 개념
엔티티 그대로 반환하면 API 응답 구조 제어가 어렵기 때문에 DTO로 변환한다.
QueryDSL에서는 크게 3가지 방식이 있다.

### (1) Projections.constructor()
```java
List<MemberDto> result = queryFactory
    .select(Projections.constructor(MemberDto.class,
        member.username,
        member.age))
    .from(member)
    .fetch();
```
- 생성자 기반 매핑
- 필드명 달라도 됨 (순서 중요)

### (2) Projections.fields()
```java
List<MemberDto> result = queryFactory
    .select(Projections.fields(MemberDto.class,
        member.username.as("name"),
        member.age))
    .from(member)
    .fetch();
```
- 필드 이름으로 매핑 (Setter 또는 필드 접근)

- alias(.as()) 필수

### (3) Projections.bean()
```java
List<MemberDto> result = queryFactory
    .select(Projections.bean(MemberDto.class,
        member.username,
        member.age))
    .from(member)
    .fetch();
```
- Setter 기반 매핑
- DTO에 setter 필요

### (4) DTO 안에 DTO (중첩 DTO)
```java
List<MemberDto> result = queryFactory
    .select(Projections.constructor(MemberDto.class,
        member.username,
        Projections.constructor(TeamDto.class,
            team.id,
            team.name)))
    .from(member)
    .join(member.team, team)
    .fetch();
```

# 3. 커스텀 페이지네이션
### 개념
Spring Data JPA의 Pageable을 그대로 쓰기 어렵거나, 복잡한 QueryDSL 쿼리에서 count 쿼리를 따로 제어해야 할 때 직접 구현한다.

### 예시
```java
public Page<MemberDto> search(Pageable pageable) {
    List<MemberDto> content = queryFactory
        .select(new QMemberDto(member.username, member.age))
        .from(member)
        .offset(pageable.getOffset())
        .limit(pageable.getPageSize())
        .fetch();

    long total = queryFactory
        .select(member.count())
        .from(member)
        .fetchOne();

    return new PageImpl<>(content, pageable, total);
}
```

# 4. transform - groupBy
### 개념
transform(groupBy())는 1:N 관계 데이터를 DTO 형태로 그룹핑할 때 사용한다.

### 예시
```java
코드 복사
Map<String, List<MemberDto>> result = queryFactory
    .from(team)
    .join(team.members, member)
    .transform(
        groupBy(team.name)
            .as(list(Projections.constructor(MemberDto.class,
                member.username,
                member.age)))
    );
```
### 결과
``` json

{
  "TeamA": [ { "username": "선아", "age": 24 }, { "username": "민수", "age": 25 } ],
  "TeamB": [ { "username": "지민", "age": 22 } ]
}
```
### 특징
QueryDSL transform 전용 기능 (SQL GROUP BY와 다름)

1:N DTO 매핑 시 유용

# 5. order by null
### 개념
ORDER BY NULL은 정렬을 명시적으로 비활성화할 때 사용한다.

### 예시
```java
queryFactory
    .selectFrom(member)
    .orderBy(Expressions.stringTemplate("NULL"))
    .fetch();
``` 
### 용도
MySQL에서 GROUP BY 후 불필요한 정렬을 제거

distinct 쿼리나 대량 조회 시 성능 개선

