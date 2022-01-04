# JUnit5 정리

## JUnit5란?

이전 버전의 `JUnit` 과 다르게 `JUnit5` 는 세가지 다른 하위 프로젝트의 여러 모듈로 구성된다

> JUnit5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

#### JUnit Platform

- `JUnit Platform` 은 `JVM` 환경에서 테스트 프레임워크를 시작하는 기반 역할을 합니다
- `Test Engine` 을 사용하여 사용자 정의의 테스트 세트를 실행할 수 있는 `JUnit Platform Suite Engine` 을 제공합니다
- 유명한 IDE(Intellij,Eclipse,NetBeans) 와 Build Tool(Gradle,Maven) 에서도 지원합니다

#### JUnit Jupiter

- `JUnit Jupiter` 는 `JUnit5` 에서 테스트 및 확장을 작성하기 위한 새로운 프로그래밍과 확장 모델의 조합입니다
- `Jupiter` 하위 프로젝트는 플랫폼에서 `Jupiter` 기반 테스트를 실행할 수 있는 `TestEngine` 엔진을 제공합니다

#### JUnit Vintage

- `JUnit Vintage` 는 `TestEngine` 플랫폼에서 `JUnit3` 및 `JUnit4` 기반 테스트를 실행할 수 있는 `TestEngine` 을 제공합니다

#### 지원하는 Java 버전

`JUnit5` 는 Java8 이상 버전이 필요합니다. 하지만 이전 버전의 `JDK` 로 컴파일된 코드는 계속 테스트할 수 있습니다

## JUnit5 사용법

### 의존성 추가

`spring-boot-starter-test` 에는 다음과 같은 라이브러리들이 포함되어 있습니다

- JUnit5
- Spring Test & Spring Boot Test
- AssertJ
- Hamcrest
- Mockito
- JSONassert
- JsonPath

위에서 설명한것처럼 `JUnit Vintage` 는 `JUnit5` 와 이전 버전을 통합하도록 설계되어 사용할 수 있습니다.
`JUnit5` 의 기능만 사용하고 싶다면 아래의 코드를 그대로 사용하면되고, 
이전 버전의 기능을 같이 사용하고 싶다면 `exclude`,`exclusions` 에 해당하는 코드를 지우면 됩니다

#### Gradle

```
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
```

#### Maven

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
      <exclusion>
        <groupId>org.junit.vintage</groupId>
        <artifactId>junit-vintage-engine</artifactId>
      </exclusion>
    </exclusions>
  </dependency>
```

