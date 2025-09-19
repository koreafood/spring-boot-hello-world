# Spring Boot Hello World REST API

Spring Boot를 사용한 간단한 REST API 프로젝트입니다.

## 기능

- **GET /api/hello**: "Hello World!" 문자열을 반환
- **GET /api/hello/json**: JSON 형태로 Hello World 메시지를 반환

## 실행 방법

### 필요 조건
- Java 17 이상
- Maven 3.6 이상

### 빌드 및 실행

```bash
# 프로젝트 컴파일
mvn clean compile

# 테스트 실행
mvn test

# 애플리케이션 실행
mvn spring-boot:run
```

### API 테스트

애플리케이션이 실행되면 다음 URL로 접속할 수 있습니다:

- http://localhost:8080/api/hello
- http://localhost:8080/api/hello/json

## 프로젝트 구조

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── helloworld/
│   │               ├── HelloWorldApplication.java
│   │               └── controller/
│   │                   └── HelloWorldController.java
│   └── resources/
│       └── application.properties
└── test/
```

## 기술 스택

- Spring Boot 3.2.1
- Java 17
- Maven
- Spring Web