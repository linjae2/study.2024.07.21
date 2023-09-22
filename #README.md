
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
```

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

    wget https://download.oracle.com/otn/java/jdk/8u202-b08/1961070e4c9b4e26a04e7f5a083f551e/jdk-8u202-linux-x64.tar.gz

    wget https://download.oracle.com/otn/java/jdk/8u202-b08/1961070e4c9b4e26a04e7f5a083f551e/jdk-8u202-linux-i586.tar.gz

    $> wget --http-user=injae@snudh.org --http-password=Hello.1254 --no-check-certificate "https://download.oracle.com/otn/java/jdk/8u381-b09/8c876547113c4e4aab3c868e9e0ec572/jdk-8u381-linux-i586.tar.gz"
    $> wget --http-user=injae@snudh.org --http-password=Hello.1254 --no-check-certificate "https://download.oracle.com/otn/java/jdk/8u381-b09/8c876547113c4e4aab3c868e9e0ec572/jdk-8u381-linux-x64.tar.gz"

    wget https://download.java.net/openjdk/jdk11.0.0.1/ri/openjdk-11.0.0.1_linux-x64_bin.tar.gz
    wget https://download.java.net/openjdk/jdk17/ri/openjdk-17+35_linux-x64_bin.tar.gz
    wget https://download.java.net/openjdk/jdk20/ri/openjdk-20+36_linux-x64_bin.tar.gz
    ```

 * [Apache Mave download](https://maven.apache.org/download.cgi)

    ```shell
    wget https://dlcdn.apache.org/maven/maven-3/3.9.4/binaries/apache-maven-3.9.4-bin.tar.gz
    ```

## [Spring Boot](https://godekdls.github.io/Spring%20Boot/spring-application/)

기동정보 로깅은 spring.main.log-startup-info를 false로 설정하면 끌 수 있다.


# 스프링 마이크로서비스 코딩 공작소

 > https://github.com/gilbutITbook/006962.git  
 > https://github.com/klimtever/spmia-chapter1.git

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