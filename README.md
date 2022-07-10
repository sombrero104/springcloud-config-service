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

## user-service 에 연동 

#### [pom.xml]
~~~
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
~~~

#### [bootstrap.yml]
~~~
spring:
  cloud:
    config:
      uri: http://127.0.0.1:8888
      name: ecommerce
~~~

#### [실행 결과 Bootstrap 로그]
<img src="./images/config_service_bootstrap_log.png" width="60%" /><br/>

#### [실행 결과 Config 정보 확인]
<img src="./images/config_service_test_result.png" width="60%" /><br/>
<br/><br/>

## Configuration 갱신 방법
- 서버 재기동 
- Spring Boot Actuator refresh 
    - 재기동 없이 갱신 가능 
    - Application 상태, 모니터링 
    - Metric 수집을 위한 Http End point 제공 
    - user-service에 Spring Boot Actuator 의존성 추가 <br/>
    #### [user-service - pom.xml]
    ~~~
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ~~~
    #### [user-service - application.yml]
    ~~~
      management:
        endpoints:
          web:
            exposure:
              include: refresh, health, beans, busrefresh, info, metrics, prometheus
    ~~~
    테스트를 하기 위해 공통으로 사용하는 ecommerce.yml 파일을 수정한 후 <br/>
    git-local-repo 로컬 리파지토리에 커밋한다. <br/>
    그리고 http://127.0.0.1:8000/user-service/actuator/refresh (POST) 로 요청을 보내면 <br/>
    아래와 같이 응답으로 어떤 내용이 변경되었는지 확인할 수 있으며, <br/>
    
    <img src="./images/request_actuator_refresh.png" width="50%" /><br/>
    
    user-service 를 재기동하지 않아도 해당 변경 내용이 반영된 것을 확인할 수 있다. <br/>
    
    <img src="./images/after_actuator_refresh_config.png" width="56%" /><br/>
    
- Spring cloud bus 사용 (Actuator 보다 효율적) <br/>
<br/><br/>

## 프로파일 적용
ecommerce.yml 파일을 프로파일을 다르게 하여 새로 추가한다. <br/>

<img src="./images/config_profile.png" width="50%" /><br/>

#### [gateway-service]
<img src="./images/config_profile_gateway_service_01.png" width="50%" /><br/>

<img src="./images/config_profile_gateway_service_02.png" width="70%" /><br/>

#### [user-service]

<img src="./images/config_profile_user_service_01.png" width="50%" /><br/>

<img src="./images/config_profile_user_service_02.png" width="70%" /><br/>

<br/><br/><br/><br/>

