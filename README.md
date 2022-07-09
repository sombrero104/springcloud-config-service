<br/>

# Spring Cloud Config
<br/>

## 로컬에 git 저장소 만들어서 테스트 
로컬에 git 저장소를 만든 후 공통으로 쓸 yml 설정 파일을 추가한다. <br/>
~~~
$ mkdir git-local-repo
$ git init .
$ vim ecommerce.yml 
    (아래 내용 추가) 
    ----------------------------
    token:
      expiration_time: 864000000
      secret: user_token
    
    gateway:
      ip: [게이트웨이 아이피]
    ----------------------------
$ git add ecommerce.yml
$ git commit -m 'upload an application  yml file'
~~~

그리고 config-service에 아래와 같이 설정한다. <br/>
#### [application.yml]
~~~
server:
  port: 8888

spring:
  application:
    name: config-service
  cloud:
    config:
      server:
        git:
          uri: file:///Users/sombrero104/workspace/git-local-repo
~~~
#### [App.java]
~~~
@SpringBootApplication
@EnableConfigServer
public class App {
    ...
}
~~~

<img src="./images/git_local_repo.png" width="41%" /><br/>

config-service를 실행한 후 아래 경로로 접속을 하면 <br/>
http://127.0.0.1:8888/ecommerce/default <br/>
아래와 같이 저장소에 추가했던 설정 파일 정보를 확인할 수 있다. <br/>

<img src="./images/ecommerce_default.png" width="57%" /><br/>
<br/><br/>

## Configuration 갱신 방법
- 서버 재기동 
- Spring Boot Actuator refresh 
    - 재기동 없이 갱신 가능 
    - Application 상태, 모니터링 
    - Metric 수집을 위한 Http End point 제공 
    - Spring Boot Actuator 의존성 추가 
- Spring cloud bus 사용 (Actuator 보다 효율적)

<br/><br/><br/><br/>

