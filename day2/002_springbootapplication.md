# Spring Boot

앞에서 살펴본 바와 같이 기존 레거시 스프링은 설정할 것이 많았다. 그런데 모던 랭귀지들이 간편한 설정들을 가지고 나오면서 스프링도 간편한 설정으로 어플리케이션을 만들기 위해서 

## SpringBootApplication 

스프링 부트 어노테이션은 이미 많은 설정 정보를 스프링 컨테이너에 전달한다. 각각의 의미에 대해서 알아보고 넘어가자.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication 
```

SpringBootConfiguration을 사용하면 Configuration을 자동으로 찾는다.

CompentScan은 어플리케이션이 위치한 디렉토리 하위의 @Component가 명시된 Bean들을 스캔해서 빈으로 컨테이너에 등록한다.

EnableAutoConfiguration은 스프링부트의 meta 파일을 읽어서, 미리 정의되어 있는 자바 설정 파일(@Configuration)들을 빈으로 등록하는 역할을 수행한다. spring.factories 라는 스프링부트의 meta파일을 읽어들인다.

