# JPA

## 시작하기 

- ORM : Object-Relation Mapping. 자바 객체와 관계형 데이터베이스 간의 매핑처리를 위한 API
- JPA : Java Persistence API(2.2 버전 이전), Jakarta Persistence API(2.2 버전 이후). ORM을 위한 자바 표준 API 스펙
- Hibernate : JPA의 구현체 중에 가장 널리 사용. JBoss사에서 개발
- 왜 ORM 혹은 JPA를 사용해야 할까? 우리는 객체 지향으로 개발하기를 원하기 떄문

## Hibernate Getting Started

[Getting Started](https://docs.jboss.org/hibernate/orm/6.1/quickstart/html_single/#hibernate-gsg-tutorial-jpa-config)

Hibernate.cfg.xml

```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>
        <!-- Database connection settings -->
        <property name="connection.driver_class">org.h2.Driver</property>
        <property name="connection.url">jdbc:h2:mem:db1;DB_CLOSE_DELAY=-1</property>
        <property name="connection.username">sa</property>
        <property name="connection.password"/>

        <!-- SQL dialect -->
        <property name="dialect">org.hibernate.dialect.H2Dialect</property>

        <!-- Disable the second-level cache  -->
        <property name="cache.provider_class">org.hibernate.cache.internal.NoCacheProvider</property>

        <!-- Echo all executed SQL to stdout -->
        <property name="show_sql">true</property>

        <!-- Drop and re-create the database schema on startup -->
        <property name="hbm2ddl.auto">create</property>

        <mapping resource="org/hibernate/tutorial/hbm/Event.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```

Event.hbm.xml

```xml
<?xml version="1.0"?>

<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="org.hibernate.tutorial.hbm">

    <class name="Event" table="EVENTS">
        <id name="id" column="EVENT_ID">
            <generator class="increment"/>
        </id>
        <property name="date" type="timestamp" column="EVENT_DATE"/>
        <property name="title"/>
    </class>

</hibernate-mapping>

```

저장하기

```java
SessionFactory sessionFactory = Persistence.createEntityManagerFactory( "org.hibernate.tutorial.jpa" );

EntityManager entityManager = sessionFactory.createEntityManager();
entityManager.getTransaction().begin();
entityManager.persist( new Event( "Our very first event!", new Date() ) );
entityManager.persist( new Event( "A follow up event", new Date() ) );
entityManager.getTransaction().commit();
entityManager.close();
```

조회하기

```java
entityManager = sessionFactory.createEntityManager();
entityManager.getTransaction().begin();

List<Event> result = entityManager.createQuery( "from Event", Event.class ).getResultList();

for ( Event event : result ) {
    System.out.println( "Event (" + event.getDate() + ") : " + event.getTitle() );
}

entityManager.getTransaction().commit();
entityManager.close();
```