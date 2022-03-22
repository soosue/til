# ORM이란?
### 시작
`ORM`이란 무엇인가? `mybatis`가 아닌 `JPA`를 쓰는 이유가 무엇일까에 대한 고민을 하던 중에 `Hibernate` 공식홈페이지에서 아주 좋은 글을 찾은 것 같아 내용을 공유해본다.

https://hibernate.org/orm/what-is-an-orm/  
https://hibernate.org/orm/

---
### ORM이란?
ORM(Object-Relational Mapping)이란, 객체를 RDBMS에 매핑해주는 것을 말하며 객체-RDBMS간의 불일치를 해결해주는 역할을 한다.

---
### Persistence(영속화)

![image](https://user-images.githubusercontent.com/54942017/159479256-868bdee2-9142-40d3-a34a-646bcb6effe7.png)

단어를 먼저 살펴보자. 영속이란 단어를 보면 영원히 계속한다는 의미를 가지고 있다. 그리고 공식문서를 보면
> `Hibernate ORM`은 `application`이 `영속화`할 수 있게 하는 것에 많은 관심을 가지고 있다.

라고 말한다. 무엇을 영원히 계속하게 한다는 것이고 어떤 의미를 가지고 있는 것일까?

`영속화`는 간단히 말하면 `application`의 데이터를 `application`의 영역 밖에서도 살아있게 하는 것이다. 자바를 예로 들자면, 특정 정보를 가진 `객체`를 먼 훗날에 똑같은 정보를 사용할 수 있도록 `JVM`의 영역을 넘어서도 살아있게 하는 것이다.

이런 의미에서 저장이라는 용어를 사용하는 것이 아니라. 영속화라는 단어를 사용한다.

---
### Relational Databases(관계형 데이터베이스)
하이버네이트 ORM은 RDBMS를 이용해 영속화를 제공한다.

---
### 객체-관계 불일치
객체모델과 관계모델이 서로 잘 맞지 않는다는 것을 `객체-관계 패러다임의 불일치`라는 말로 사용한다. RDBMS는 데이터를 테이블 형식으로 나타내고, 객체지향언어는 서로 연결된 객체 그래프로 데이터를 나타내기 때문이다.

관계형 DB를 이용해서 객체의 그래프를 가져오고 저장하는 방법에는 5가지 불일치 되는 점이 있다.

### 세분화
객체 모델을 설계하다 보면 실제로 상응하는 테이블보다 많을 수 있다는 점이 불일치 중 하나이다. 
~~~java
class User {
  String name;
  Address address;
}

class Address {
  ...
}
~~~
~~~mysql
create table User (
  name,
  address
)
~~~

### 상속
OOP에서 상속은 당연하게 받아들여진다. 하지만 RDBMS에서는 정의되어있지 않다.(몇몇 DB는 가능하지만 표준은 아니다.)

### 식별자
RDBMS에서는 pk가 같으면 같은 값이다. 하지만 자바에서는 동일성(==), 동등성(equals) 이렇게 2가지가 존재한다. 이 또한 불일치하는 부분이다.

### 연관관계
OOP에서 연관관계는 단방향이다. 하지만 RDBMS에서는 fk로 어느쪽이던 찾을 수 있다. 자바에서 양방향으로 하고 싶다면, 관계를 두번 정의해줘야 한다.
~~~java
class User {
  List<Post> posts;
}

class Post {
}

User user = new User();
user.posts;

// 단방향
User -> Post o
User <- Post x
~~~
~~~java
class User {
  List<Post> posts;
}

class Post {
  User user;
}

User user = new User();
user.posts;

Post post = new Post();
post.user;

// 양방향, Post에도 ser와의 관계를 적어줘야한다.
User -> Post o
User <- Post o
~~~

### 데이터 탐색
자바에서는 한 객체에서 시작해서 쭉 찾아가야 한다. 하지만 RDBMS에서는 JOIN을 통해서 몇몇 엔티티들은 생략하고 원하는 정보만 찾을 수 있다.
~~~java
class User {
  List<Post> posts;
}

class Post {
  List<Comment> comments;
}

class Comment {
  String content;
}

String content = new User().posts.comments.content;
~~~
~~~mysql
select c.content
from user u
    join post p on u.user_id = p.user_id
    join comment c on p.post_id = c.post_id
where u.user_id = 1;
~~~
