# Kafka란

카프카는 `Source Application` 과 `Target Application` 의 커플링을 약하게 하기 위해 탄생했습니다.
`Source Application` 는 `Kafka` 에 데이터를 전송하면 되고 `Target Application` 는 `Kafka` 에서 데이터를 가져오면 됩니다.
`Source Application` 는 클릭로그, 결제로그 등을 보낼 수 있고 `Target Application` 는 로그 적재, 로그 처리를 할 수 있습니다.
`Source Application` 에서 `Kaffa`에 보낼 수 있는 데이터는 거의 제한이 없습니다. `Kafka` 는 데이터를 적재하는 `Topic` 이라는 개념 있는데 `Queue` 라고 생각하시면 됩니다.
`Topic` 에 데이터를 넣는 역할을 하는 것은 `Kafka Producer` 가 하고 `Topic` 에서 데이터를 가져가는 역할을 하는 것은 `Kafka Comsumer` 가 처리한다.
`Producer` 와 `Comsumer` 는 라이브러리로 되어있어서 구현이 가능하다. `Kafka` 는 고가용성으로 서버가 이슈가 생기거나 갑작스럽게 서버가 내려가는 경우에도 손실 없이 데이터를 복구 할 수
있습니다. 그러기 때문에 빅데이터 처리에서는 `Kafka` 를 사용하는 곳이 없다고 봐도 무방하다.





