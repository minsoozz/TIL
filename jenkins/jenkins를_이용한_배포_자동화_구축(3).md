# Jenkins를 이용한 배포 자동화(3) - Publish Over SSH 를 이용한 원격 서버 배포

> Publish over SSH 플러그인이 2022년 1월 12일 부로 젠킨스 보안 정책으로 인해 배포가 중단되었습니다.
>
> https://www.jenkins.io/security/advisory/2022-01-12/

Publish Over SSH plugin 을 사용하면 원격 서버에 SSH를 통한 배포를 할 수 있습니다.

## SSH란?

SSH란 `Secure Shell Protocol` 의 약자로 네트워크 상의 다른 컴퓨터에 로그인 하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해주는 응용 프로그램 또는 그
프로토콜은 가리킵니다.

## 1. Publish Over SSH 설치

2022년 2월 현재 젠킨스 플러그인 관리를 통한 설치가 불가능 하기 때문에 직접 `.hpi` 확장자를 다운로드 하여 설치해야 합니다.

https://archives.jenkins-ci.org/plugins/publish-over-ssh/latest/ 에 접속하여 `publish-over-ssh.hpi` 파일을 다운로드 합니다.

`Jenkins 관리` -> `플러그인 관리` -> `고급` -> `플러그인 올리기` 으로 이동하여 다운로드 파일을 첨부 후 `올리기` 버튼을 클릭하면 설치를 수동으로 진행할 수 있습니다.

![hpi.png](hpi.png)

## 2. Jenkins 서버에서 SSH key 발급

젠킨스가 설치되어 있는 서버에서 `jenkins` 계정으로 로그인 한 뒤 아래의 명령어를 입력합니다. (passphrase는 따로 설정을 하지 않을 것이기 때문에 Enter를 입력하여 생략하시면 됩니다)

```
ssh-keygen
```

![ssh-keygen](../images/ssh-keygen.png)

명령어 입력이 완료 되었다면 /home/jenkins/.ssh 경로에 `id_rsa`, `id_rsa.pub` 파일이 2개 생성됩니다.

- id_rsa: private key
    - 절대로 타인에게 노출이 되면 안된다
- id_rsa.pub: public key
    - 접속하려는 원격 서버의 authorized_key에 입력한다

## 3. Publish Over SSH 설정

`Jenkins 관리` -> `시스템 설정` -> `Publish Over SSH` 탭에서 추가 버튼을 클릭하고 아래의 정보를 입력합니다.

- Name: SSH Server 를 구분할 수 있는 이름
- HostName: SSH를 이용해 파일을 전송하려는 원격 서버
- Username: 원격 서버 계정명
- Remote Directory: 원격 서버 디렉토리 입력

고급 탭으로 이동하여 `Use password authentication, or use a different key` 에 체크한 뒤 Jenkins 서버에서 생성한 `id_rsa` 내용을 아래의 명령어를 통해 복사하여
입력합니다.

```
cat /home/jenkins/.ssh/id_rsa
```

![publish_over_ssh](../images/publish_over_ssh.png)

## 4. 원격 서버 설정

비밀번호 대신 원격 서버에 접속하려면 .ssh 디렉토리에 authorized_key 파일이 필요합니다. 원격 서버에서 아래의 명령어를 입력하여 파일을 생성합니다.

.ssh 디렉토리가 없는 경우

```
mkdir ~/.ssh
```

```
vi ~/.ssh/authorized_key
```

`authorized_key` 파일에 젠킨스 서버에서 발급받은 `id_rsa.pub` 내용을 복사하여 입력합니다.

```
cat /home/jenkins/.ssh/id_rsa.pub
```

.ssh 디렉토리는 중요한 정보가 담긴 디렉토리이기 때문에. Permission 권한을 수정해주어야 합니다. (권한이 맞지 않으면 SSH 인증이 동작하지 않습니다)

```
chmod 700 ~/.ssh
chmod 644 ~/.ssh/authorized_keys
```

원격 서버까지 설정이 모두 끝났다면 다시 Jenkins로 이동하여 `Test Configuration` 을 클릭하여 정상적으로 인증에 성공하는지 테스트 합니다.

![test_configuration](../images/test_configuration.png)

## 5. Jenkins Item 설정

이전 포스팅에서 생성한 `Item` 의 구성으로 이동합니다.

`Branches to build` -> `Branch Specifier (blank for 'any')`

특정 `Branche` 를 `Build` 하고 싶은 경우 작성합니다.

`빌드 유발` -> `GitHub hook trigger for GITScm polling` 체크

`Github Repository`의 `Push` 가 발생하면 `Build` 를 유발시킬 것이므로 체크합니다.

![item_setting1](../images/item_setting1.png)

`Build` -> `Invoke Gradle script` 선택

- Use Gracle Wrapper 선택
- Make gradlew executable 체크

`Tasks` -> 오른쪽 역삼각형 클릭 -> 아래의 명령어 입력.

```
clean 
build 
```

![item_setting2](../images/item_setting2.png)

`빌드 후 조치` -> `빌드 후 조치 추가` -> `Send build artifacts over SSH` 선택

- Source files: 원격 서버로 보낼 파일의 경로를 입력.
    - ant 패턴으로 `**/*.jar` 방법도 가능합니다
- Remove prefix: Sourece file에서 지정한 경로에서, 제거 할 prefix를 지정
- Remote directory: Source file가 저장될 원격 서버의 경로
    - 시스템 설정에서 설정한 경로의 이후를 입력해야 합니다
- Exec command: 파일 전송이 모두 끝난 이후 원격 서버에서 실행할 명령어
    - 시스템 설정에서 설정한 계정으로 접속하기 때문에 특정 명령어에 대한 권한 문제가 발생할 수 있습니다

글쓴이는 `Docker Container` 를 이용하기 때문에 다음과 같이 명령어를 입력하였습니다.

준비된 스크립트나 명령어가 있다면 이 부분에 입력하시면 됩니다.

![item_setting3](../images/item_setting3.png)

설정이 모두 끝났으면 `main branch` 에 `Push` 를 진행하여 `Item Build History` -> `Console Ouput` 에서 정상적으로 `Build` 에 성공하였는지 확인합니다.

![success](../images/success.png)

`Publish Over SSH` 를 이용한 원격 서버 배포과정이 모두 끝났습니다.

`Jenkins` 와 `Slack` 을 연동하여 `Build` 과정을 알림 받을 수 있는 방법은 다음 포스팅에 이어서 진행하겠습니다.