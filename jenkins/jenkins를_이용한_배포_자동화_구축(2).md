# Jenkins를 이용한 배포 자동화(2) - Jenkins & Github 연동 방법

Jenkins와 Github 연동은 Github에서 제공하는 Github Webhook 을 이용하여 연동할 수 있습니다

아직 Jenkins 환경이 구축되지 않았다면 `이전` 글에서 확인할 수 있습니다

## Github Webhook 이란?

Github Webhook은 특정 이벤트가 발생했을 때 등록한 URL로 HTTP POST Payload를 보내 이슈 트래커를 업데이트하거나, CI Build를 트리거 할 수 있습니다. 또한 Github
Webhook는 역방향 API라고 불리는데 일반적인 API는 요청을 하면 정보를 제공하지만, Github Webhook은 등록한 URL로 정보를 보내줍니다.

이번 포스팅에서는 Repository의 특정 Branch에 Push Event가 발생하면 Github Webhook을 통해 Jenkins Server에 빌드를 유발하도록 구축해보겠습니다