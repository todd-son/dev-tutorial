# Controller에 대한 이해

## 브라우저에 https://www.naver.com 을 입력했을때 페이지가 어떻게 노출되는지 생각해보자.  

1. 브라우저의 DNS서버에 질의하여, 도메인으로 목적지 IP를 찾아낸다.
2. 별도 포트가 명시되지 않았으므로 80포트(디폴트)로 요청이 전송된다.
3. 해당 서버에 80 포트를 열어놓은 어플리케이션이 해당 요청을 받고 적절한 응답을 리턴한다.

위의 질문에 뒤에 스프링 어플리케이션이 있다고 생각하고 http://localhost:8080/api/members/{memberId} 을 처리하는 과정을 생각해보자.

1. localhost의 127.0.0.1 즉 본인의 컴퓨터로 요청이 전송된다.
2. 본인의 컴퓨터의 8080 포트를 열어놓은 스프링 어플리케이션으로 요청이 전송된다.
3. DispatcherServlet이 해당요청의 해석 해서 적절한 컨트롤러의 메소드로 요청을 전송한다.
4. 해당 메소드의 실행결과가 전달된다.

## HTTP의 요청은 어떤 구조로 되어 있을까?

1. 시작줄 RequestLine은 아래와 같다. HTTP Method와 URL과 프로토콜 버전으로 이루어진다. 

```http request
GET http://developer.mozilla.org/en-US/docs/Web/HTTP/Messages HTTP/1.1
```

2. 헤더는 대소문자 구분없는 문자열 다음에 콜론(':')이 붙으며, 그 뒤에 오는 값은 헤더에 따라 달라진다.

```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
```

3. 본문은 요청의 마지막 부분에 들어간다. 모든 요청에 본문이 들어가지는 않습니다. GET, HEAD, DELETE , OPTIONS처럼 리소스를 가져오는 요청은 보통 본문이 필요가 없습니다. 일부 요청은 업데이트를 하기 위해 서버에 데이터를 전송한다. 보통 (HTML 폼 데이터를 포함하는 또는 JSON) POST 요청일 경우에 그렇다.

```
{ "name" : "todd", "company" : cacao }
```

## 리소스 네이밍

우리는 앞으로 매우 다양한 API를 만들 것이다. 여기에 어떠한 형식으로 API를 표현할 것인지에 대한 본인만의 원칙 및 가이드가 필요하다. 아래는 요즘 유행하는 REST API의 가이드를 정리한 사이트이다.

https://restfulapi.net/

그리고 일반적으로 REST에서는 Http Method는 POST(생성), PUT(수정), PATCH(행동, 부분 변경), GET(조회), DELETE(삭제)에 활용한다. 

https://www.techtarget.com/searchapparchitecture/tip/The-5-essential-HTTP-methods-in-RESTful-API-development


## 어노테이션

그리고 어노테이션의 무엇이며, 어떻게 해서 이러한 동작들이 가능한지 살펴보자. 어노테이션은 실제적으로는 아무런 동작을 하지 않는 것이다. 말 그대로 주석일 뿐 실제 동작은 리플렉션에 의해 이루어진다. 

## HTTP Request 매핑

@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping 등의 어노테이션을 이용해서 URI Pattern를 매핑할 수 있다.

@PathVariable, @RequestParam, @RequestBody, @RequestHeader 등의 어노테이션을 활용하여 각 Http의 요소들을 캡쳐할 수 있다.

https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-controller

```java
    @PostMapping("/members/{memberId}/sample")
    public MemberDto sample(
            @PathVariable("memberId") Long memberId,
            @RequestParam("name") String name,
            @RequestBody MemberDto memberDto) {
        return new MemberDto(memberDto.getId(), name);
    }
```





