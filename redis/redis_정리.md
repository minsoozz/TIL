# redis 정리

> 포스팅 내용은 강대명님의 우아한 테크 세미나 내용을 정리 하였습니다

## `Redis` 를 소개하기 전에 `Cache` 란?

### `Cache` 의 정의

`Cache` 는 나중에 요청올 결과를 미리 저장해두었다가 빠르게 서비스 해주는 것을 의미

### `Cache` 구조 #1 - Look aside cache

1. Web Server는 데이터가 존재하는지 `Cache` 를 연계 확인
2. `Cache` 에 데이터가 있으면 `Cache` 에서 가져온다
3. `Cache` 에 데이터가 없다면 DB에서 얻어온다
4. `DB` 에서 얻어온 데이터를 `Cache` 에 다시 저장한다

### `Cache` 구조 #2 - Write Back

1. Web Server는 모든 데이터를 `Cache` 에 저장
2. `Cache` 에 특정 시간동안의 데이터가 저장
3. `Cache` 에 있는 데이터를 DB에 저장한다.
4. DB에 저장된 데이터를 삭제한다.

- `Write Back` 의 단점은 메모리에 저장이 되기 때문에 장애가 생기면 데이터가 사라질 가능성이 있다
- `Log` 를 DB에 관리 할 때 특정 시간동안의 로그를 모아 Batch 작업으로 Insert Query를 날려주는데 `Write Back` 구조를 많이 사용한다

## `Redis` 소개

### Remote dictionary server

- In-Memory Data Structure Store
- Open Source
- Support data structures
    - Strings, set, sorted-set, hashes, list
    - Hyperloglog, bitmap, geospatial index
    - Stream

## 왜 `Collection` 이 중요한가?

- 외부의 `Collection` 을 잘 이용하는 것으로, 여러가지 개발 시간을 단축시키고, 문제를 줄여줄 수 있기 때문에 `Collection` 이 중요

### 개발의 편의성

#### 랭킹 서버를 직접 구현한다면?

- 가장 간단한 방법
    - DB에 유저 Score를 저장하고 Score로 order by로 정렬 후 읽어오기
    - 개수가 많아지면 속도에 문제가 발생할 수 있음
        - 결국은 디스크를 사용하므로

- `Redis` 의 `Sorted Set` 을 이용하면, 랭킹을 구현 할 수 있음
    - 덤으로, Replication도 가능하다
    - 다만 가져다 쓰면 거기에 한계에 종속덕이 됨
        - 랭킹에 저장해야할 id가 1개당 100byte라고 했을 때
            - 10명 1K
            - 10000명 1M
            - 10000000명 1G
            - 10000000000 명 1TB

### 개발의 난이도

#### 친구 리스트를 직접 구현한다면?

- 친구 리스트를 KEY/VALUE 형태로 저장해야 한다면?
    - 현재 유저 123의 친구 Key는 friends:123, 현재 친구 A가 있는 중


- Transaction 1(친구 B를 추가)
    - 친구 리스트 friends:123을 읽는다
    - friends123의 끝에 친구 B를 저장한다
    - 해당 값을 friend:123에 저장한다


- Transaction 1(친구 C를 추가)
    - 친구 리스트 friends:123을 읽는다
    - friends123의 끝에 친구 C를 추가한다
    - 해당 값을 friend:123에 저장한다

### Race Condition 해결

> Race Condition이란 여러 개의 Thraed가 경헙하는 것, Context Switching에 따라 원하지 않는 결과가 발생

- Redis는 기본적으로 Single Threaded
- `Redis` 의 경우 자료구조가 `Atomic` 하기 때문에, 해당 `Race Condition` 을 피할 수 있습니다.
    - 그래도 잘못짜면 발생한다
- 서로 다른 Transaction Read/Write를 동기화

## `Redis` 는 실행했는데 어디에 써야 하죠?

### `Redis` 사용처

- Remote Data Store
    - A서버, B서버, C서버에 데이터를 공유하고 싶을 때
- 한대에서만 필요하다면, 전역 변수를 쓰면 되지 않을까?
    - `Redis` 자체가 `Atomic` 을 보장해준다(Single Thread)
- 주료 많이 쓰는 곳들
    - 인증 토큰 등을 저장(String or hash)
    - Ranking Board(Sorted Set)
    - User API Limit
    - Job Queue(List)

## Redis Collections

### Strings

#### 단일 Key

- 기본 사용법
    - `Set <key> <value>`
    - `Get <key>`

#### 멀티 Key

- 기본 사용법
    - `mset <key1> <value1> <key2> <value2> ..... <keyN> <valueN>`
    - `mget <key1> <key2> ..... <keyN>`

#### Strings - 간단한 SQL을 대체한다면?

- `insert into users(name,email) values('minsoo','minsoo1604@naver.com')`
- Using Set
    - `Set name:minsoo minsoo`
    - `Set email:minsoo minsoo1604@naver.com`
- Using mset
    - `Mset name:minsoo minsoo email:minsoo minsoo1604@naver.com`

### List

#### insert

- 기본 사용법
    - `Lpush <key> <A>`
        - `Key:(A)`
    - `Rpush <key> <B>`
        - `Key:(A,B)`
    - `Lpush <key> <C>`
        - `Key:(C,A,B)`

#### pop

- 기본 사용법
    - `Key:(C,A,B,D,A)`
    - `LPOP <key>`
        - `Pop C, Key:(C,B,D,A)`
    - `RPOP <key>`
        - `Pop A, Key:(A,B,D)`
    - `RPOP <key>`
        - `Pop D, Key:(A,B)`

#### lpop,blpop,rpop,brpop

- 기본 사용법
    - `Key:()`
    - `LPOP <Key>`
        - `No Data`
    - `BLPOP <key>`
        - `누가 데이터를 Push 하기 전까지 대기`

### Set

#### 데이터가 있는지 없는지만 체크하는 용도

- 기본 사용법
    - `SADD <key> <value>`
        - `value가 이미 key에 있으면 추가되지 않는다`
    - `SMEMBERS <key>`
        - `모든 value를 돌려줌`
    - `SISMEMBER <key> <value>`
        - `value가 존재하면 1, 없으면 0`

### Sorted Set

- 기본 사용법
    - `ZADD <key> <Score> <value>`
        - `value가 이미 Key에 있으면 Score로 변경된다`
    - `ZRANGE <key> <StartIndex> <endIndex>`
        - `해당 Index 범위 값을 모두 돌려줌`
            - `Zrange testkey 0 -1`
                - `모든 범위를 가져옴`

### Hash

#### Key 밑에 sub key가 존재

- 기본 사용법
    - `Hmset <key> <subkey1> <value1> <subkey2> <value2>`
    - `Hgetall <key>`
        - `해당 key의 모든 subkey와 value를 가져옴`
    - `Hget <key> <subkey>`
    - `Hmget <key> <subkey1> <subkey2> ..... <subkeyN>`

#### 간단한 SQL을 대체 한다면?

- `insert into users(name,email) values('minsoo','minsoo1604@naver.com')`
- `hmset minsoo name minsoo email minsoo1604@naver.com`

### `Collection` 주의 사항

- 하나의 컬렉션에 너무 많은 아이템을 담으면 좋지 않음
    - 10000개 이하 몇천개 수준으로 유지하는게 좋음
- Expire는 Collection의 item 개별로 걸리지 않고 전체 Collection에 대해서만 걸림
    - 즉 해당 10000개 아이템을 가진 Collection에 expire가 걸려있다면 그 시간 후에 10000개의 아이템이 모두 삭제

## `Redis` 운영

### 메모리 관리를 잘하자

- `Redis` 는 In-Memory Data Store
- Physical Memory 이상을 사용하면 문제가 발생
    - Swap 이 있다면 Swap 사용으로 해당 메모리 Page 접근시 마다 늦어짐
    - Swap 이 없다면?
- Maxmemory를 설정하더라도 이보다 더 사용할 가능성이 큼
- RSS 값을 모니터링 해야함

#### 메모리 관리

- 큰 메모리를 사용하는 instance 하나보다는 적은 메모리를 사용하는 instance 여러개가 안전함
- `Redis` 는 메모리 파편화가 발생할 수 있음. 4.x 대 부터 메모리 파편화를 줄이도록 jemlloc에 힌트를 주는 기능이 들어갔으나, jemalloc 버전에 따라서 다르게 동작할 수 있음
- 3.x 대 버전의 경우
    - 실제 userd memory는 2GB로 보고가 되지만 11GB의 RSS를 사용하는 경우가 자주 발생

#### 메모리가 부족할 때는?

- Cache is Cash
    - 좀 더 메모리 많은 장비로 Migration
    - 메모리가 빡빡하면 Migration 중에 문제가 발생할수도 있다
- 있는 데이터 줄이기
    - 데이터를 일정 수준에서만 사용하도록 특정 데이터를 줄임
    - 다만 이미 Swap을 사용중이라면, 프로세스를 재시작 해야함

#### 메모리를 줄이기 위한 설정

- 기본적으로 Collection 들은 다음과 같은 자료구조를 사용
    - Hash -> HashTable을 하나 더 사용
    - Sorted Set -> Skiplist와 HashTable을 이용
    - Set -> HashTable 사용
    - 해당 자료구조들은 메모리를 많이 사용함
- Ziplist를 이용하자

### O(N) 관련 명령어는 주의하자

- `Redis` 는 `Single Threaded
    - 그러면 Redis가 동시에 여러 개의 명령을 처리할 수 있을까?
        - 한번에 하나의 명령만 수행 가능
    - 참고로 단순한 get/set의 경우, 초당 10만 TPS 이상 가능(CPU 속도에 영향을 받습니다)

- 대표적인 O(N) 명령어
    - KEYS
    - FLUSHALL, FLUSHDB
    - Delete Collections
    - Get All Collections

- 대표적인 실수 사례
    - Key가 백만개 이상인데 확인을 위해 KEYS 명령을 사용하는 경우
    - Spring security oauch RedisTokenStore
        - 최신 버전에서는 사용 가능

- KEYS 는 어떻게 대체할 것인가?
    - scan 명령을 사용하는 것으로 하나의 긴 명령을 짧은 여러번의 명령으로 바꿀 수 있다

- Spring security oauch RedisTokenStore 이슈
    - Access Token의 저장을 List(O(N)) 자료구조를 통해서 이루어짐
        - 검색,삭제시에 모든 item을 매번 찾아봐야 함
        - 100만개 쯤 되면 전체 성능에 영향을 줌
    - 현재는 Set(O(1))을 이용해서 검색, 삭제를 하도록 수정되어있음

## Replication

### Async Replication

- Replication Lag 이 발생할 수 있다.
- Replicationof(>=5.0.0) or "slaveof" 명령으로 설정 가능
    - Replicationof hostname port
- DBMS로 보면 statement replication가 유사

### Replication 설정 과정

- Secondary에 replicaof or slaveof 명령을 전달
- Secondary는 Primary에 sync 명령 전달
- Primary는 현재 메모리를 저장하기 위해
    - Fork
- Fork 한 프로세서는 현재 메모리 정보를 disk에 dump
- 해당 정보를 secondary에 전달
- Fork 이후의 데이터를 secondary에 계속 전달

### Redis Replication 시 주의할 점

- Replication 과정에서 fork 가 발생하므로 메모리 부족이 발생할 수 있다
- Redis-cli--rdb 명령은 현재 상태의 메모리 스냅샷을 가져오므로 같은 문제를 발생시킴
- AWS나 클라우드의 Redis는 좀 다르게 구현되어서 좀더 해당 부분이 안정적
- 많은 대수의 Redis 서버가 Replica를 두고 있다면
    - 네트워크 이슈나, 사람의 작업으로 동시에 replication이 재시도 되도록 하면 문제가 발생할 수 있음

## 권장 설정 Tip

- Maxclient 설정 50000
- RDB/AOF 설정 off
- 특정 commands disable
    - Keys
    - AWS의 ElasticCache는 이미 하고 있음
- 전체 장애의 90% 이상이 EKYS와 SAVE 설정을 사용해서 발생
- 적절한 ziplist 설정

### Redis 데이터 분산

- 데이터의 특성에 따라서 선택할 수 있는 방법이 달라진다
    - Cache 일때는 우아한 Redis
    - Persistent 해야하면 안 우아한 Redis

### 데이터 분산 방법

- Application
    - Consistent Hashing
        - twemproxy 를 사용하는 방법으로 쉽게 사용 가능
    - Sharding
- Redis Cluster

## 결론

- 기본적으로 Redis는 매우 좋은 툴
- 그러나 메모리를 빡빡하게 쓸 경우, 관리하기 어려움
    - 32GB 장비라면 24GB 이상 사용하면 장비 증설을 고려하는 것이 좋음
    - Write가 Heavy 할 때는 migration도 매우 주의해야함
- Client-output-buffer-limit 설정이 필요

### Redis as Cache

- Cache 일 경우는 문제가 적게 발생
    - Redis가 문제가 있을 때 DB둥의 부하가 어느정도 증가하는지 확인 필요
    - Consistent Hashing도 실제 부하를 아주 균등하게 나누지는 않음. Adaptive Consistent Hashing을 이용해 볼 수도 있음

### Redis as Persistent Store

- Persistent Store의 경우
    - 무조건 Primary/Secondary 구조로 구성이 필요함
    - 메모리를 절대로 빡빡하게 사용하면 안됨
        - 정기적인 migration이 필요
        - 가능하면 자동화 툴을 만들어서 이용
        - RDB/AOF가 필요하다면 Secondary에서만 구동

### 주의해야 할 점

- Single Thread 서버 이므로 시간 복잡도를 고려해야 한다
    - O(N) 명령 주의
- In-memory 특성상 메모리 파편화, 가상 메모리등 이해가 필요하다

