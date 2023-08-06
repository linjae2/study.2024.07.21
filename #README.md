# study

token : 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

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