---
layout: post
title: Spring Data, Spring Data JPA, JPA, Hibernate, JDBC, DBCP 용어정리
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



- Spring Data로 프로젝트를 개편하던 중에 검색시 Order By를 해야할 일이 있었습니다. 그런데 'JPA order by'로 검색할 지, 'hibernate order by'로 검색할 지, 'Spring Data order by'로 검색할 지 고민이 됐습니다.
- 이 외에도, 가벼운 클론 코딩을 맛보고 프로젝트에 들어가니 클론 코딩 때 쏟아진 다양한 기술들이 서로 엉켜 각 기술의 관계나 역할을 제대로 정립하지 못한 것 같아 용어와 역할 등을 정리해둡니다.



## 용어 정리

---

### JPA

- Java Persistence API 로, 자바 어플리케이션과 RDB가 함께할 때 사용되는 인터페이스입니다. 
- 즉, 자바 어플리케이션에 RDB를 붙이고 싶으면 JPA를 구현해서 붙이라는 것입니다.
- 인터페이스이기 때문에 구현해줘야 합니다.
- Java기술이기 때문에 공식 명세는 oracle 홈페이지에서 찾을 수 있습니다. javax.persistence 패키지입니다. [여기](https://docs.oracle.com/javaee/7/api/javax/persistence/package-summary.html)를 참고해주세요.



### Hibernate

- JPA를 구현한 기술들 중 하나입니다. Hibernate 외에도 JPA를 구현한 다양한 기술들이 있습니다.
- Hibernate ORM이라고도 불리는데 ORM은 Object-relational mapping로 데이터베이스 어플리케이션과 객체지향 어플리케이션이 호환될 수 있도록 데이터를 변환하는 프로그램입니다.
- JPA의 인터페이스를 구현합니다. 
- 소개부터 구현 클래스까지 [여기](https://hibernate.org/orm/documentation/5.4/)에 자세히 설명되어 있습니다.



### Spring Data

- Spring Data는 자바어플리케이션에서 데이터를 다뤄야 하는 기술들을 Spring과 연동하기 쉽게 모듈화 해놓은 기술들입니다.
- JPA를 Spring과 연동하기 쉽게 묶어둔 게 Spring Data JPA이고, 이 외에도 Spring Data JDBC, Spring Data MongoDB, Spring Data Redis 등 다양한 Spring Data family가 있습니다.



### Spring Data JPA

- Spring Data JPA는 JPA를 Spring과 연동하기 쉽도록 묶어둔 것입니다. 즉, Spring Data JPA 자체 역시 인터페이스입니다.
- Spring Data JPA에서 기본 구현체로 Hibernate로 사용합니다.



### JDBC

- JDBC는 Java DataBase Connectivity로, 자바 애플리케이션에서 DB에 연결하기 위한 인터페이스입니다.
- 자바애플리케이션과 데이터베이스를 연결해주는 인터페이스라는 점에서 JPA와 유사하지만 JPA는 데이터를 변환해주고 JDBC는 데이터베이스 연결을 해줍니다.
- 인터페이스이기 때문에 역시 구현체가 필요합니다.



### DBCP

- DBCP는 DataBase Connection Pool로, 데이터베이스의 커넥션을 관리하는 기술입니다. 
- 주로 사용되는 기술은 Apache의 Commons DBCP, BoneCP, HikariCP 등이 있습니다.