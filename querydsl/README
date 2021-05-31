# Querydsl

## 기본 설정

프로젝트 생성 및 초기 설정

1. [start.spring.io](https://start.spring.io/) 에서 통해 프로젝트 생성
2. 의존성 추가
   * Spring Web
   * Spring Data JPA
   * H2 Database
   * Lombok
3. querydsl 설정
   * build.gradle 설정
      ```groovy
      plugins {
         id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
      }
      
      dependencies {
         implementation 'com.querydsl:querydsl-jpa'
      }
      
      //querydsl 추가 시작
      def querydslDir = "$buildDir/generated/querydsl"
      querydsl {
         jpa = true
         querydslSourcesDir = querydslDir
      }
      sourceSets {
         main.java.srcDir querydslDir
      }
      configurations {
         querydsl.extendsFrom compileClasspath
      }
      compileQuerydsl {
         options.annotationProcessorPath = configurations.querydsl
      }
      //querydsl 추가 끝
      ```
      * [querydsl 플러그인 버전](https://plugins.gradle.org/plugin/com.ewerk.gradle.plugins.querydsl)
4. QClass 생성 확인
   * `Gradle - other - compileQuerydsl`
5. spring-boot 설정
   * application.yml 설정
      ```yaml
      spring:
        datasource:
          url: jdbc:h2:tcp://localhost/~/querydsl
          username: sa
          password:
          driver-class-name: org.h2.Driver
        jpa:
          hibernate:
            ddl-auto: create
          properties:
            hibernate:
              # show_sql: true
              format_sql: true
              use_sql_comments: true # JPQL 출력
     
      logging.level:
        org.hibernate.SQL: debug
        # org.hibernate.type: trace
      ```
   * query parameter 라이브러리 추가(`application.yml`)
      ```groovy
      dependencies {
        implementation("com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.7.1")
      }
      ```
      * [p6spy 버전 확인](https://github.com/gavlyukovskiy/spring-boot-data-source-decorator)
    
## 기본 Q-Type 활용

### Q클래스 인스턴스를 사용하는 2가지 방법

```java
QMember member = new QMember("m");
QMember member = QMember.member;
```
* QMember 생성자의 파라미터는 JPQL의 테이블 Alias로 같은 테이블 조인 시 이름이 같을 수 있으므로 Alias를 변경해야 하는 상황에 사용

### 검색 조건 쿼리

```java
eq : equals
ne : not equals
eq().not() : not equals
        
isNotNull
        
in : in(10, 20)
notIn : 
between
        
goe
gt
loe
lt
        
like
contains
startsWith
```

### AND 조건

```java
@Test
public void searchAndParam() {
    List<Member> result1 = queryFactory
        .selectFrom(member)
        .where(member.username.eq("member1"),
            member.age.eq(10))
        .fetch();
    assertThat(result1.size()).isEqualTo(1);
}
```

* 이 경우 null 값은 무시 -> 메서드 추출을 활용해서 동적 쿼리를 깔끔하게 만들 수 있음

### 결과 조회

* fetch() : 리스트 조회, 데이터 없으면 빈 리스트 반환
* fetchOne() : 단 건 조회
    * 결과가 없으면 : null
    * 결과가 둘 이상이면 : com.querydsl.core.NonUniqueResultException
* fetchFirst() : limit(1).fetchOne()
* fetchResults() : 페이징 정보 포함, total count 쿼리 추가 실행
* fetchCount() : count 쿼리로 변경해서 count 수 조회

