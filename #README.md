
1. [스프링 마이크로서비스 코딩 공작소](#스프링-마이크로서비스-코딩-공작소)

1. [spmia-chapter7](#spmia-chapter7)

1. [spmia-chapter8](#spmia-chapter8)

1. [spmia-chapter9](#spmia-chapter9)


# study

token : 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

* GitHub Access Tocken 연결

   ```shell
   $> git clone https://<user-id>:<token>@<repository url>
   ```

# ubuntu 22.04

```shell
$> lsb_release -a
```

## VS Code git 연동

## 인텔리제이 설치

```shell
$> sudo tar xf ideaIC-2023.2.tar.gz -C /opt/
$> /opt/idea-IC-232.8660.185/bin/idea.sh
``````

 하단의 톱니바뀌 버튼을 클리하고 Create Desktop Enty(바로가기 생성) 버튼을 클릭한다.

## Java 설치 및 환경 변수 설정하기

 * openjdk-8-jdk

    ```shell
    # Java 설치 확인하기
    $> java -version
    $> javac -version

    # JAVA_HOME settings
    $> sudo vi ~/.bashrc

    export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
    export PATH=$PATH:$JAVA_HOME/bin
    ```


 * java jdk download [홈페이지](https://jdk.java.net/java-se-ri/20)

    ```shell
    wget https://download.java.net/openjdk/jdk8u43/ri/openjdk-8u43-linux-x64.tar.gz
    wget https://download.java.net/openjdk/jdk11.0.0.1/ri/openjdk-11.0.0.1_linux-x64_bin.tar.gz
    wget https://download.java.net/openjdk/jdk17/ri/openjdk-17+35_linux-x64_bin.tar.gz
    wget https://download.java.net/openjdk/jdk20/ri/openjdk-20+36_linux-x64_bin.tar.gz
    ```


# 스프링 마이크로서비스 코딩 공작소

## spmia-chapter6

 > 스프링 클라우드와 주울로 서비스 라우팅

 * 빌드 및 실행
 
   ```
   $> mvc clean package docker:build

   $> sudo apt purge --auto-remove redis-server
   $> docker container rm -f $(docker container list -qa)

   $> docker-compose -f docker/common/docker-compose.yml up -d
   ```


 * zuul 실습

   ```java
   @RestController
   public class TestController {
      @GetMapping("/ping")
      public ResponseEntity<String> healthCheck() {
         return ResponseEntity.ok("pong");
      }
   }
   ```

   ```shell
   $> java -jar application.jar --server.port=8081 &
   $> java -jar application.jar --server.port=8082 &
   ```

zuul을 사용하기 위한 Annotation

- @EnableZuulProxy
- @EnableZuulServer


* Routing 설정

  ```yaml
  server:
  port: 8080

  zuul:
    routes:
      service-1:
        path: /service-1/**
        url: http://localhost:8081
      service-2:
        path: /service-2/**
        url: http://localhost:8082
  ```

## spmia-chapter7

스프링 시큐리티를 적용하는 과정에서 jwt 토큰 생성 중 발생한 에러이다  
해당 문제는 jdk 11 이상 버전에서는 관련 모듈이 기본 참조되지 않아 에러가 발생

 * zuulsvr/pom.xml

   ```xml
   <!-- https://mvnrepository.com/artifact/javax.xml.bind/jaxb-api -->
   <dependency>
     <groupId>javax.xml.bind</groupId>
     <artifactId>jaxb-api</artifactId>
     <version>2.3.1</version>
   </dependency>
   ```

 * 빌드 및 실행

   ```
   $> mvc clean package docker:build

   $> sudo apt purge --auto-remove redis-server
   $> docker container rm -f $(docker container list -qa)

   $> docker-compose -f docker/common/docker-compose.yml up -d
   ```

## spmia-chapter8

 * confsvr/src/main/docker/Dockerfile

   ```shell
   RUN cd /tmp/ && \
       curl -k -LO "http://download.oracle.com/otn-pub/java/jce/8/jce_policy-8.zip" -H 'Cookie: oraclelicense=accept-securebackup-cookie' && \
       unzip jce_policy-8.zip && \
       rm jce_policy-8.zip && \
       yes |cp -v /tmp/UnlimitedJCEPolicyJDK8/*.jar /usr/lib/jvm/java-1.8-openjdk/jre/lib/security/
   ADD @project.build.finalName@.jar /usr/local/configserver/
   ```
   위와 같은 경우 docker 이미지 빌드시 오류가 발생(아래와 같이 수정)

   ```shell
   RUN cd /tmp/ && \
       curl -L -b "oraclelicense=a" http://download.oracle.com/otn-pub/java/jce/8/jce_policy-8.zip -O && \
       unzip jce_policy-8.zip && \
       rm jce_policy-8.zip && \
       yes |cp -v /tmp/UnlimitedJCEPolicyJDK8/*.jar /usr/lib/jvm/java-1.8-openjdk/jre/lib/security/
   ADD @project.build.finalName@.jar /usr/local/configserver/
   ```

 * 빌드 및 실행

   ```
   $> mvc clean package docker:build

   $> sudo apt purge --auto-remove redis-server
   $> docker container rm -f $(docker container list -qa)

   $> docker-compose -f docker/common/docker-compose.yml up -d
   ```


## spmia-chapter9

```
$> mvc clean package docker:build

$> docker-compose -f docker/common/docker-compose.yml up -d
```