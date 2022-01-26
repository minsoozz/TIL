# Jenkins를 이용한 배포 자동화(2) - Jenkins & Github 연동 방법

Jenkins와 Github 연동은 Github에서 제공하는 Github Webhook 을 이용하여 연동할 수 있습니다

아직 Jenkins 환경이 구축되지 않았다면 [Jenkins를 이용한 배포 자동화(1) - Docker로 Jenkins 설치 방법](jenkins/jenkins를_이용한_배포_자동화_구축(1).md) 에서 확인할 수
있습니다

## Github Webhook 이란?

Github Webhook은 특정 이벤트가 발생했을 때 등록한 URL로 HTTP POST Payload를 보내 이슈 트래커를 업데이트하거나, CI Build를 트리거 할 수 있습니다. 또한 Github
Webhook는 역방향 API라고 불리는데 일반적인 API는 요청을 하면 정보를 제공하지만, Github Webhook은 등록한 URL로 정보를 보내줍니다.

이번 포스팅에서는 Repository의 특정 Branch에 Push Event가 발생하면 Github Webhook을 통해 Jenkins Server에 빌드를 유발하도록 구축해보겠습니다

## 1. GitHub Integration Plugin 설치

젠킨스 메인화면 좌측의 Jenkins 관리 -> 플러그인 관리 메뉴를 클릭합니다

![jenkins_plugin.png](../images/jenkins_plugin.png)

설치가능 탭에서 `GitHub Integration` 을 검색하고 `Download now and install after restart` 버튼을 눌러 설치합니다

![github_integration.png](../images/github_integration1.png)

플러그인 적용을 위해 `설치가 끝나고 실행중인 작업이 없으면 Jenkins 재시작.` 항목에 체크를 하고 Jenkins를 재시작합니다. 만약 설치가 완료되어도 반응이 없거나, 체크박스에 클릭을 미처하지 못했다면
아래의 URL로 이동하여 Jenkins를 재시작 할 수 있습니다

```
호스트 IP:9999/restart
```

![github_integration2.png](../images/github_integration2.png)

## 2. SSH key 발급

젠킨스가 설치되어있는 서버로 이동하여 아래의 명령어를 입력한다. 필자는 jenkins 라는 계정을 생성하여 해당 디렉토리에 생성하였다. 지금은 다른 설정을 할 필요가 없으므로 Enter 버튼을 계속 눌러 진행하면
된다

```
ssh-keygen
```

![ssh_keygen.png](../images/ssh_keygen.png)

public key와 private key가 생성되었다면 public key를 복사한다.

```
cat .ssh/id_rsa.pub
```

## 3. Github SSH key 등록

Github에 로그인하여 우측 상단의 프로필을 클릭하고 `Setting` 으로 이동 후 `SSH and GPG keys` 메뉴에서 `New SSH Key` 버튼을 클릭하여 위에서 복사한 id_rsa.pub 내용을
붙혀넣기 한다

> Repository의 Setting 메뉴에서도 SSH key를 등록할 수 있지만 다른 Repository 에서도 해당 Jenkins Server를 이용할 것이므로 계정에 등록해줍니다

![github_ssh](../images/github_ssh.png)

## 4. Repository Webhook 설정

배포 자동화를 구축할 Repository로 이동하여 `Setting` -> `Webhooks` -> `Add Webhooks` 를 클릭하여 다음과 같이 입력해준다

- 호스트 IP:9999/github-webhook/
- Content type : application/json
- Just the push event, Active 체크박스 활성화

> Payload URL 마지막에 / 기입을 하지 않으면 Webhooks Trigger시 오류가 발생합니다

![github_webhook](../images/github_webhook.png)

등록 완료 후 `Recent Deliveries` 탭으로 이동하여 통신에 성공하였는지 확인합니다.

![webhook_success.png](../images/webhook_success.png)

## 6. Jenkins Credentials 설정

`Jenkins` 의 메인 화면 에서 좌측의 `새로운 item` -> `FreeStyle project` 를 선택하고 `OK` 버튼을 눌러 다음 화면으로 이동한다

![new_item.png](../images/new_item.png)

소스 코드 관리 탭에서 `Git` 을 선택하고 Repository URL을 기입해준다

> Repository URL은 Github Repository 주소로 이동하여 Code 버튼을 클릭하면 확인할 수 있다. 꼭 SSH 경로를 기입해야한다

![jenkins_source_code_manage](../images/jenkins_source_code_manage.png)

![github_ssh](../images/github_ssh_clone.png)

SSH 경로를 붙혀넣기하면 `git@github.com: Permission denied (publickey)` 과 같은 오류가 발생한다. `Credentials` -> `Add` 를 클릭한다

- ID : Credentials의 ID를 적절하게 작성하면 된다
- Description : Credentials의 설명을 작성
- Username : Github ID를 입력해야 한다
- Private Key : Enter directly 클릭 후 `id_rsa` private key를 기입해주어야 한다

```
cat .ssh/id_rsa.pub
```

> -----BEGIN RSA PRIVATE KEY-----, -----END RSA PRIVATE KEY----- 영역까지 모두 복사해야 한다

![add_credentials.png](../images/add_credentials.png)

만약 정상적으로 등록이 되었다면 아래의 화면처럼 오류 문구 없이 연동이 완료된다.

![credentials_success](../images/credentials_success.png)


