# cannot find symbol 오류

```
C:\Users\minsoo\IdeaProjects\notice\build\generated\querydsl\com\example\notice\entity\QAttachments.java:17: error: cannot find symbol
public class QAttachments extends EntityPathBase<Attachments> {
                                                 ^
  symbol: class Attachments
```

`Intellij IDEA` 를 이용한 프로젝트에서 `compileQuerydsl` 후 실제로 `Qdomain` 이 존재하지만 `build` 를 하면 `cannot find symbol public class Q~`
라는 문구와 오류가 발생하고 `build` 에 실패하는 문제.

오류를 해결하기 위한 여러가지 방법을 소개해드리겠습니다.

## 개발환경

---

- Intellij IDEA
- Java 8
- Gradle 7.2
- Windows 10 64bit

## Gradle 설정

---

```
plugins {
    id 'org.springframework.boot' version '2.6.0'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    //querydsl 추가
    id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
    id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'

    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'

    implementation group: 'commons-io', name: 'commons-io', version: '2.4'

    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }

    //querydsl 추가
    implementation 'com.querydsl:querydsl-jpa'
    implementation 'com.querydsl:querydsl-apt'

    // redis 추가
    implementation 'org.springframework.boot:spring-boot-starter-data-redis'
}

test {
    useJUnitPlatform()
}

//querydsl 추가
def querydslDir = "src/main/generated/"
querydsl {
    library = "com.querydsl:querydsl-apt"
    jpa = true
    querydslSourcesDir = querydslDir
}
sourceSets {
    main.java.srcDir querydslDir
}
configurations {
    querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}
```

## 1. Intellij IDEA Annotation processor 설정

`Gradle` 에서 추가한 `querydsl-apt` 라이브러리는 