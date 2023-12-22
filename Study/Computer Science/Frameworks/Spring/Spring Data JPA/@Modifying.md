#java #jpa


- @Query 어노테이션(JPQL Query, Native Query)을 통해 작성된 INSERT, UPDATE, DELETE (SELECT 제외) 쿼리에서 사용되는 어노테이션이다.
- 기본적으로 JpaRepository에서 제공하는 메서드 혹은 메서드 네이밍으로 만들어진 쿼리에는 적용되지 않는다.
- clearAutomatically, flushAutomatically 속성을 변경할 수 있으며 **주로 벌크 연산과 같이 이용**된다.
- JPA Entity Life-cycle을 무시하고 쿼리가 실행되기 때문에 해당 어노테이션을 사용할 때는 영속성 컨텍스트 관리에 주의해야 한다. (clearAutomatically, flushAutomatically를 통해 간단하게 해결할 수 있다.)

### **Bulk 연산**

벌크 연산이란, 단건 UPDATE, DELETE 연산을 제외한 다건의 UPDATE, DELETE 연산을 하나의 쿼리로 하는 것을 의미한다. JPA에서 단건 UPDATE 같은 경우에는 Dirty Checking(변경 감지)를 통해서 수행되거나 save()를 통해 수행할 수 있다. DELETE 경우 단건, 다건 쿼리 메서드로 제공된다.

여기서는 **@Modifying과 @Query를 사용한 벌크연산**을 알아본다.

이 방법의 장점은 JPQL을 자유롭게 정의하여 사용할 수 있고, 하나의 쿼리로 많은 데이터를 변경할 수 있다는 점이다.

@Query에 벌크 연산 쿼리를 작성하고, @Modifying 어노테이션을 붙이지 않으면 InvalidDataAccessApiUsageException이 발생한다.

### **clearAutomatically**

이 attribute는 @Modifying이 붙은 해당 쿼리 메서드 실행 직후, 영속성 컨텍스트를 clear할 것인지 지정하는 attribute이다. default 값은 false이다. 하지만 default 값은 false이다. 하지만 default 값인 false로 사용할 경우, 영속성 컨텍스트의 1차 캐시와 관련된 문제점이 발생할 수 있다.

#### **문제점**

JPA에서는 영속성 컨텍스트에 있는 **1차 캐시**를 통해 엔티티를 캐싱하고, DB의 접근 횟수를 줄임으로써 성능을 개선한다. 1차 캐시는 @Id값을 key 값으로 엔티티를 관리한다. 그래서 findById 등을 통해 **엔티티를 조회**했을 시, @Id 값이 1차 캐시에 존재한다면 DB에 접근하지 않고, 캐싱된 엔티티를 반환한다. 

그렇다면, 벌크 연산을 통해 데이터 변경 쿼리를 실행하고, 해당 엔티티를 조회하면 어떤 일이 발생할지 예측해보자.

1. 엔티티 객체를 하나 생성한다. (transient 상태)

2. 객체의 초기값을 설정한다.

3. 해당 엔티티의 Repository를 통해 해당 엔티티 객체를 save() 메서드를 통해 저장한다. (persistent 상태)

4. 벌크 연산을 통해 기존에 설정한 초기값을 변경한다. (SQL 실행 / 이때, flush가 발생하여 해당 쿼리 메서드가 실행되기 전의 쿼리들이 모두 flush 된다.)

5. findById()를 통해 해당 엔티티를 조회한다.

#### **예제**

```java
// Article.java
@Entity
@Getter
@Setter
public class Article {

    @Id
    @GeneratedValue
    private Long id;
    
    private String title;

    private String content;
}
```

```java
// ArticleRepository.java
public interface ArticleRepository extends JpaRepository<Article, Long> {

    @Modifying
    @Query("UPDATE Article a SET a.title = :title WHERE a.id = :id")
    int updateTitle(Long id, String title);
}
```

title를 update하는 쿼리 메서드를 정의했다. @Modifying과 @Query를 통해 직접 JPQL을 정의했다. clearAutomatically는 default인 false이다.

```java
@ExtendWith(SpringExtension.class)
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class Test {

    @Autowired
    private ArticleRepository articleRepository;
    
    @org.junit.jupiter.api.Test
    @Rollback(false)
    void update() {
        Article article = new Article(); // article - Transient 상태
        article.setTitle("before");
        articleRepository.save(article); // article - Persistent 상태
        
        int result = articleRepository.updateTitle(1L, "after"); // UPDATE SQL
        assertThat(result).isEqualTo(1);
        
        System.out.println(articleRepository.findById(1L).get().getTitle()); // "before"이 출력
    }
}
```

참고) @DataJpaTest를 사용하면 자동으로 embeded DB로 대체되어서 실제 DB를 확인할 수 없게 된다. AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)은 embeded DB가 아닌 properties에 정의한 DataSource를 사용할 수 있게 한다.

참고) @DataJpaTest는 기본적으로 @Transactional을 포함하고 있다. 그래서 각 테스트 메서드를 실행할 때, 데이터가 Rollback이 된다. 데이터가 Rollback이 실행된다면 실제 DB에서 테이블을 확인할 수 있으므로 @Rollback(false)으로 처리해주었다.

실제 DB에서는 title이 "after"로 변경되었지만, 메서드 안에서는 여전히 "before"이다.

```java
// ArticleRepository.java
public interface ArticleRepository extends JpaRepository<Article, Long> {

    @Modifying(clearAutomatically = true)
    @Query("UPDATE Article a SET a.title = :title WHERE a.id = :id")
    int updateTitle(Long id, String title);
}
```

clearAutomatically를 true로 설정해주었다.

after가 출력되는 것을 알 수 있다. after로 출력되기 전에 SELECT Query가 실행된다. 이는 1차 캐시가 아닌 DB에서 Article을 조회했기 때문이다.

#### **결론**

JPA의 1차 캐시는 DB에 접근 횟수를 줄여 성능을 개선하는 좋은 기능이지만 @Modifying과 @Query를 이용한 벌크 연산에서는 이 기능때문에 예측하지 못한 결과가 나올 수 있다. 

JPA에서 조회를 실행할 때, 1차 캐시를 확인해서 해당 엔티티가 1차 캐시에 존재한다면 DB에 접근하지 않고, 1차 캐시에 있는 엔티티를 반환한다. 하지만 **벌크 연산은 1차 캐시를 포함한 영속성 컨텍스트를 무시하고 바로 Query를 실행**하기 때문에 영속성 컨텍스트는 데이터 변경을 알 수 없다. 즉, 벌크 연산을 실행할 대 1차 캐시(영속성 컨텍스트)와 DB의 데이터 싱크가 맞지 않게 되는 것이다.

하지만, **@Modifying의 clearAutomatically를 true로 변경해준다면, 벌크 연산 직 후 자동으로 영속성 컨텍스트를 clear** 해준다. 그러면 조회를 실행하면 1차 캐시에 해당 엔티티가 존재하지 않기 때문에 DB 조회 쿼리를 실행하게 된다. 이렇게 함으로써 데이터 동기화 문제를 해결할 수 있다.

### **flushAutomatically**

이 Attribute는 @Query와 @Modifying을 통한 쿼리 메서드를 사용할 때, 해당 쿼리를 실행하기 전, 영속성 컨텍스트의 변경사항을 DB에 flush할 것인지를 결정하는 Attribute이다. default 값은 false이다.

#### **Hibernate의 FlushModeType**

Spring Data JPA는 구현체로 Hibernate를 사용하고 있다. Spring Data JPA를 통해 Hibernate를 사용한다. 그 Hibernate에는 FlushModeType enum class가 있다. 그 값으론 AUTO와 COMMIT이 있고 default 값은 AUTO이다.

AUTO(default): flush가 쿼리 실행 시 발생한다.

COMMIT: flush가 트랜잭션이 commit 될 때 발생한다.

Hibernate의 FlushModeType가 default로 AUTO 였기 때문에 flushAutomatically가 false여도 쿼리 실행 전 flush가 나간다.

#### **Hibernate로 직접 테스트**

```java
@Entity
@Getter
@Setter
public class Article {
    
    @Id
    @GeneratedValue
    private Long id;
    
    private String title;
    
    private String content;
    
    private Boolean isPublished = false;
    
    public void publish() {
        isPublished = true;
    }
}
```

```java
public interface ArticleRepository extends JpaRepository<Article, Long> {

    @Modifying
    @Query("DELETE FROM Article a WHERE a.isPublished = TRUE")
    int deletePublic();
}
```

EntityManager는 JPA에서 컨텍스트 매니저를 관리하는 interface이다. 

Session은 Hibernate의 핵심이 되는 API이다. EntityManager를 상속받고 있다. 

entityManager.unwrap(Session.class)를 통해 Session을 직접 사용할 수 있다.

```java
@Test
@DisplayName("JPA(Hibernate)를 통한 테스트 - FlushModeType.AUTO")
@Rollback(false)
void delete2() {
    Session session = entityManager.unwrap(Session.class);
    
    Article article = new Article(); // Transient
    session.save(article); //Persist
    
    Article byId = session.find(Article.class, 1L); // get by Persistence Context (NOT DB)
    byId.publish();
    
    int delete = session.createQuery("DELETE FROM Article a WHERE a.isPublished = TRUE").executeUpdate();
    
    assertThat(delete).isEqualTo(0);
}
```

FlushModeType.AUTO를 그대로 사용해보았다. 테스트는 실패한다.

```java
@Test
@DisplayName("JPA(Hibernate)를 통한 테스트 - FlushModeType.AUTO")
@Rollback(false)
void delete2() {
    Session session = entityManager.unwrap(Session.class);
    session.setFlushMode(FlushModeType.COMMIT); // COMMIT으로 변경
    
    Article article = new Article(); // Transient
    session.save(article); //Persist
    
    Article byId = session.find(Article.class, 1L); // get by Persistence Context (NOT DB)
    byId.publish(); // isPublished를 true로 변경
    
    int delete = session.createQuery("DELETE FROM Article a WHERE a.isPublished = TRUE").executeUpdate();
    
    assertThat(delete).isEqualTo(0);
}
```

DELETE 쿼리가 실행되기 전 flush가 되지 않기 때문에 INSERT, UPDATE 쿼리는 실행되지 않았고 바로 DELETE 쿼리가 실행된다. 그러므로 쿼리 메서드의 반환값이 0이 된다. commit이 되는 시점에 flush가 되고 이때 INSERT, UPDATE 쿼리가 실행된다. 

#### **Spring Data JPA로 테스트**

application.yml에서 flushMode를 COMMIT으로 설정해줄 수 있다.

```yml
spring:
  jpa:
    properties:
      org:
        hibernate:
          flushMode: COMMIT
```

#### **결론**

Spring Data JPA는 구현체로 Hibernate를 사용한다. Hibernate의 FlushModeType의 default값은 AUTO이다.

@Modifying(flushAutomatically = false)여도 해당 쿼리 메서드 실행 전 flush가 실행된다.

flushAutomatically가 의미를 가리게 하려면 Hibernate의 FlushModeType의 값이 COMMIT이어야 한다.