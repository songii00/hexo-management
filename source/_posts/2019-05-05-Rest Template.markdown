---
layout: post
title:  "RestTemplate"
date:   2019-05-05 22:00:46 +0900
categories: network
subtitle : RestTemplate 알아보기
tags: [java,network]
feature-img: "assets/img/banner.jpg"
---
## RestTemplate이란

* spring 3.0 부터 지원한다. 
* Spring에서 제공하고 있는 JdbcTemplate이나, JmsTemplate과 같은 맥락의 Template
* http 통신에 유용하게 쓸 수 있는 템플릿
* RESTful Service 호출과 관련된 여러 메소드를 제공하여 REST 클라이언트를 쉽게 개발할 수 있도록 도와준다.

<br>

### 동작 순서

![동작순서](/assets/images/post/190505/(1).png) 

<!-- more -->

#### 장점

* HTTP 서버와의 통신을 단순화시킨다.
* RESTful 원칙을 지킨다.
* 기계적이고 반복적인 코드를 최대한 줄여준다.
* json, xml 를 쉽게 응답받을 수 있다. 

아래와 같이 6개의 HTTP Method를 제공한다.

| DELETE | delete(java.lang.String, java.lang.Object...) | |
|:--------:|:--------|:--------|
| GET |  getForObject (java.lang.String, java.lang.Class, java.lang.Object...) | get 요청을 보내고 ResponseEntity로 반환받음 |
| GET | getForEntity (java.lang.String, java.lang.Class, java.lang.Object...) |  |
| HEAD |  headForHeaders (java.lang.String, java.lang.Object...) |  |
| OPTIONS | optionsForAllow (java.lang.String, java.lang.Object...) |  |
| POST | postForLocation (java.lang.String, java.lang.Object, java.lang.Object...) |  |
| POST | postForObject (java.lang.String, java.lang.Object, java.lang.Class, java.lang.Object...) | post 요청을 보내고 ResponseEntity로 반환받음 |
| PUT | put (java.lang.String, java.lang.Object, java.lang.Object...) |  |
| any | exchange (java.lang.String, org.springframework.http.HttpMethod, org.springframework.http.HttpEntity, java.lang.Class, java.lang.Object...) | 헤더세팅해서 HTTP Method로 요청보내고 ResponseEntity로 반환받음 |
| any | execute (java.lang.String, org.springframework.http.HttpMethod, org.springframework.web.client.RequestCallback, org.springframework.web.client.ResponseExtractor, java.lang.Object...) |  |

### HttpClient

org.springframework.http.client 패키지에 있다. 

HttpClient는 HTTP를 사용하여 통신하는 범용 라이브러리이고, RestTemplate은 HttpClient 를 추상화(HttpEntity의 json, xml 등)해서 제공해준다. 

따라서 내부 통신(HTTP 커넥션)에 있어서는 Apache HttpComponents 를 사용한다. 


### 예제

```java
public ApiClient() {     
        <I, O > ResultWrapper callApi(I input, String uri, ParameterizedTypeReference < ResultWrapper < O >> outputType) {
                try {
                    HttpEntity<I> request = new HttpEntity<I>(input, jsonHeaders);         
                    ResponseEntity<ResultWrapper<O>> response = restTemplate.exchange(membershipUrl + uri, method, request, outputType);
                    if (HttpStatus.OK == response.getStatusCode()) {
                        return ResultWrapper.success();
                    }
                } catch (Exception e) {
                    throw new Exception(uri, e.getMessage());
                }
                throw new Exception(uri, FAIL_MESSAGE);
            }
```

###### 출처 <br/>
###### [https://sjh836.tistory.com/141][link01] <br/>
###### [https://cakas.tistory.com/9][link02]


[link01]: https://sjh836.tistory.com/141
[link02]: https://cakas.tistory.com/9

<br>

