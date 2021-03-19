---
layout: post
title: "Spring Data JPA 연습"
categories:
  - Springboot
tags:
  - Springboot
  - JPA
---


코드로 배우는 스프링 부트 웹 프로젝트를 공부하면서 jpa를 사용해 보면서 공부했던 내용들을 정리

db는 mysql을 사용했고 db연동은 완료되었다는 가정하에 시작한다

entity 패키지를 추가하고 Memo라는 클래스를 정의해주었다

```java
import lombok.*;
import javax.persistence.*;

@Entity
@Table(name = "tbl_memo")
@ToString
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Memo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) //키 자동생성
    private Long mno;

    @Column(length = 200, nullable = false)
    private String memoText;
}
```

"tbl\_memo"라는 table을 생성해주었다 

@Id를 사용하여 primarty key를 설정해준다

mno는 @GeneratedValue라는 어노테이션을 활용하여

직접 입력해주는 값이 아닌 자동으로 생성되는 번호를 사용해주었다. 

@column을 이용하여  다양한 속성을 지정해줄 수 있다 nullable, name, length 등을 이용한다.

columnDefinition을 이용하면 기본값 지정 가능

Lombok의 @Getter을 이용해서 Getter 메서드 생성 @Builder를 이용해서 객체 생성

@Builder을 이용하기 위해서는 @AllArgsConstructor와 @NoArgsConstructor를 항상 같이 처리 

코드를 실행하고 DDL을 보면

![placeholder](https://blog.kakaocdn.net/dn/tAAfP/btqUTnHawfc/nuvfukRl34L5psTGxKktD0/img.png "Medium example image")

테이블 이름이 name 속성 값으로 정해진 tbl\_memo로 잘 나온 것을 확인할 수 있다.

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAh8XT%2FbtqUYGsapkg%2F4uLRoqPNjsSMjGXkvGSsrK%2Fimg.png "Medium example image")

mysql에서 show tables와 desc tbl\_memo로 확인해봤을 때 설정한 대로 잘 들어간 것을 알 수 있다

### **JpaRepository**

---

JpaRepository는 인터페이스이고 Spring Data JPA는 이를 상속하는 인터페이스를 선언하는 것으로 모든 처리가 끝난다고 한다

reposiroty 패키지를 생성하고 MemoRepository 인터페이스를 추가해주었다

```
import org.springframework.data.jpa.repository.JpaRepository;
import com.example.demo.entity.Memo;

public interface MemoRepository extends JpaRepository<Memo,Long> {
}

```

JpaRepository를 사용할 때는 엔티티의 타입 정보(여기선 Memo 클래스 타입)와 @Id의 타입을 지정

선언 만으로 자동으로 스프링의 빈으로 등록된다

### **테스트 코드를 이용하여 crud 연습**

---

JpaRepository의 경우 다음과 같은 메서드를 활용

-   insert = save(엔티티 객체)
-   select = findByld(키 타입), getOne(키 타입)
-   update = save(엔티티 객체)
-   delete = deleteByld(키 타입), delete(엔티티 객체)

#### **insert**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import com.example.demo.entity.Memo;

import java.util.Optional;
import java.util.stream.IntStream;

@SpringBootTest
public class MemoRepositoryTests {

    @Autowired
    MemoRepository memoRepository;

    @Test
    public void testInsertDummies(){
        IntStream.rangeClosed(1,100).forEach(i -> {
           Memo memo = Memo.builder().memoText("Sample"+i).build();
           memoRepository.save(memo);
        });
    }
 }
```

1-100개의 새로운 Memo 객체를 생성 후 MemoRepository를 이용하여 insert 해준다

테스트를 실행하면 Hibernate가 발생하는 insert 구문을 확인 가능

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbiS6R0%2FbtqURufLQXG%2FW9ADTRyA7kcvVk7cbzKh41%2Fimg.png "Medium example image")



mysql에서 select로 확인했을 때도 잘 들어간 것을 알 수 있다

**select**

select에는 findById()나 getOne()을 이용하여 엔티티 객체 조회 가능

```java
    @Test
    public void testSelect(){

        Long mno = 100L;
        Optional<Memo> result = memoRepository.findById(mno);
        System.out.println("----");
        if(result.isPresent()) {
            Memo memo = result.get();
            System.out.println(memo);
        }
    }
```

위 코드는 findById로 작성된 것으로 findById를 실행한 순간에 이미 SQl이 처리가 되어

밑에 결과를 보면 ----이 sql이후에 처리된 것을 확인할 수 있다

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc2ITuv%2FbtqUUp5O5KA%2FnRqHn4uofKF4rnNn5AByKK%2Fimg.png "Medium example image")

getOne()의 경우에는 @Transational 어노테이션이 추가로 필요하고

실제 객체가 필요한 순간까지 sql을 실행하지 않아 ---이 먼저 나올 것으로 예상된다

**update**

```java
    @Test
    public void testUpdate(){
        Memo memo = Memo.builder().mno(100L).memoText("update Text").build();
        System.out.println(memoRepository.save(memo));
    }
```

100번의 Memo 객체를 만들고, save()를 호출한다

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbSsK5A%2FbtqURu7SYLQ%2FgYIIiHX88xiiZFpKek2HuK%2Fimg.png "Medium example image")

결과를 보면 select를 먼저 실행하여 값이 있는지 확인한 후에 update를 진행해준다

만약 없을 시 insert를 진행해주는데 100 대신 201을 넣어 확인해주면 

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fosb7q%2FbtqURuz6fzC%2FMk6Ty4AuuprAOp7wRY9Y8K%2Fimg.png "Medium example image")

update 대신 insert가 실행된 것을 확인할 수 있다 

**delete**

```java
    @Test
    public void testDelete(){
        Long mno = 100L;
        memoRepository.deleteById(mno);
    }
```

delete를 진행하면 select를 먼저 실행해 값을 찾고 값이 있으면 delete를 실행해준다

이때 select 값이 없으면 예외를 발생한다

100번의 값을 일단 삭제해주었다 

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbspGoA%2FbtqUOCyqzVM%2FmnZlM1Ozs94GI9YNMP2bf1%2Fimg.png "Medium example image")


이렇게 한 다음 1부터 100까지의 값을 삭제시켜 주었다  



```java
    @Test
    public void testDelete2(){
        IntStream.rangeClosed(1,100).forEach(i -> {
            memoRepository.deleteById((long) i);
        });
    }
```

이때 i는 int이기 때문에 long으로 바꾸어서 실행해준다 

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FqZEB6%2FbtqURvTiglU%2FShmtbjmdVIybSLHhjHXTG1%2Fimg.png "Medium example image")

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb6UEuF%2FbtqUUqDGppJ%2FE84iRjvxzJwsemZTdUCnJ1%2Fimg.png "Medium example image")

잘 진행되다가 오류가 발생한다 이 이유는 위에서 100을 이미 삭제해주어서   
100이 없기 때문에 발생하는 오류

![placeholder](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdxPOdF%2FbtqUOBsELYk%2FkehAkZLTDdSXvnAPKCkq70%2Fimg.png "Medium example image")

1부터 99는 잘 삭제되었다는 것을 알 수 있다