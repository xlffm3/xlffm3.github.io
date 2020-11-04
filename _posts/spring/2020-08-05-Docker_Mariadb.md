---
title: "Spring Boot와 Maria DB 연동 (Windows Docker Toolbox)"
categories:
  - Spring & Spring Boot
  - Docker
tags:
  - Spring & Spring Boot
  - Docker
toc: true
toc_sticky: true
last_modified_at: 2020-08-05T18:22:00-05:00
---

## 1. Docker Toolbox

Docker가 없어도 Spring을 DB랑 연동하는 실습은 충분히 할 수 있으나, 편의를 위해 Docker를 사용한다. 문제는 Windows OS에서 Docker를 설치하려면 여간 복잡하다는 점이다. WSL2 업데이트에 각종 설정을 건들여야 하는데, 잘 동작하지 않아서 결국 Docker Toolbox를 사용하게 되었다.

WSL2에서 Docker Desktop을 사용하고 싶다면 [여기](https://blog.naver.com/PostView.nhn?blogId=ilikebigmac&logNo=222007741507)를 참조하길 바란다. Docker Toolbox로도 충분히 원하는 기능을 사용할 수 있으니 귀찮다면 Docker Toolbox를 설치하자. (Mac 사면 이런 걱정도 안 함...)

<br>

## 2. Spring Boot 설정

> build.gradle

```gradle
compile(“org.mariadb.jdbc:mariadb-java-client”)
```

JDBC Starter과 더불어서 Maria DB 의존성을 추가해준다.

> application.properties

```properties
spring.datasource.url=jdbc:mariadb://{host}:{port}/{db_name}
spring.datasource.driverClassName=org.mariadb.jdbc.Driver
spring.datasource.username={user_name}
spring.datasource.password={password}
```

위와 같은 형식으로 properties에 설정을 추가해 주면 된다. 나는 다음과 같이 설정했다.

> application.properties

```properties
spring.datasource.url=jdbc:mariadb://192.168.99.100:3306/TESTDB
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.username=tester
spring.datasource.password=pass
```

주의할 점은 Docker Toolbox를 사용할 때 host에 **localhost**가 아닌 **기본 Docker IP**를 입력해야 한다.

기본 Docker IP는 Docker Toolbox를 실행하면 고래 그림 밑에 나오며, ```docker-machine ip default```로도 확인이 가능하다. 대부분이 **192.168.99.100**이다.

Mac에서 Docker를 사용할 때 localhost를 입력해도 문제가 없어서, Windows에서 습관적으로 localhost를 입력했더니 DB 연결이 계속 실패해서 시간을 엄청 날려먹었다.

WSL2 + Docker Desktop이면 모르겠으나, Docker Toolbox는 VirtualBox에 Linux 가상 머신을 돌리는 개념이다보니 기본적으로 192.168.99.100이 되는 것 같다.

Mac 유저라면 고민없이 localhost를 입력하면 된다. (주륵.. ^_ㅠ)

<br>

## 3. Docker Container 실행 및 접속

Docker에 대한 개념과 명령어를 알고 있으면 쉽겠지만, 모르고 있어도 무방하다.

```bash
docker pull mariadb:latest
```

먼저, Docker Toolbox를 실행하고 컨테이너 생성에 필요한 이미지를 Docker Hub에서 다운받는다.

```bash
docker run --name mariadb -d -p 3306:3306 -e MYSQL_DATABASE=TESTDB -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_USER=tester -e MYSQL_PASSWORD=pass mariadb
```

컨테이너를 실행할 때 ```--name``` 옵션을 주어 컨테이너 이름을 mariadb로 설정했다.

``-p`` 옵션의 포트 번호와 ``MYSQL_DATABASE``, ``MYSQL_USER``, ``MYSQL_PASSWORD`` 등 옵션의 Value는 properties에 본인이 설정한 것과 맞춰준다. ``MYSQL_ROOT_PASSWORD``는 본인이 원하는 대로 설정한다.

<br>

## 4. 실행

Spring Boot Application을 실행하거나, 테스트를 통해 DB가 잘 연결되었는지 확인한다. DB 목록이나 세부 테이블을 보고 싶다면 다음과 같이 실행중인 컨테이너에 bash로 접속하고, mysql에 로그인한다.

```bash
docker exec -it mariadb /bin/bash
mysql -u tester -p
```

이후 부터는 SQL 명령어로 세부 작업을 수행할 수 있다.

<br>

---

## Reference

* [Docker Toolbox - Localhost not working](https://stackoverflow.com/questions/42866013/docker-toolbox-localhost-not-working/42886035)
