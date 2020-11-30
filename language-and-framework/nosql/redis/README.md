# Redis

## Data Type

* string
* list
* set
* sorted set
* hash

데이터 타입별 Big-O 연산 언급 이쓴ㄴ경우 참고용

> O\(1\) &lt; O\(log n\) &lt; O\(n\) &lt; O\(n log n\) &lt; O\(n²\) &lt; O\(2ⁿ\) &lt; O\(n!\) &lt; O\(nⁿ\)

### 1. String

* 일반적인 **text 형식** 뿐만 아니라 `integer`와 같은 숫자나 JPEG같은 `Binary file` 뿐만 아니라 직렬화된 루비 **객체** 까지 저장 가능
* 최대 길이는 512MB

### 2. List

* 삽입 순서에 따라 정렬된 `string` 목록
* 목록의 head \(`LPUSH`\)와 tail\(`RPUSH`\)에 새 요소를 삽입 가능
* `List`의 양 극단\(head/tail\)근처의 데이터에 접근은 매우 빠르지만 `O(n)`연산이므로 매우 큰 `List`의 중간에 액세스 하려고 하면 느릴 수 있다.

  > ```bash
  > LPUSH mylist a   # now the list is "a"
  > LPUSH mylist b   # now the list is "b","a"
  > RPUSH mylist c   # now the list is "b","a","c" (RPUSH was used this time)
  > ```

### 3. Set

* 정렬되지 않은 `String` 모음
* 시간복잡도는 `O(1)`로 포함된 요소 수에 관계없이 일정하게 멤버를 추가, 제거가능
* 중복을 허용하지 않는다 \(멤버 추가시 값이 존재하는지 확인 후 추가 작업을 수행한다는 뜻\)

> 블로그를 방문하는 모든 고유 IP 주소를 얻고자 할때와 같이 중복없는 값을 얻고자 할때 유용

### 4. Sorted Set

* 정렬되지 않은 `String` 모음
* `Set`과 달리 `Score`라는 필드가 추가되어 오름차순으로 내부 정렬된다

### 5. Hash

* `field / value` 쌍으로 구성되므로 객체를 나타내는데 유용한 데이터 유형 \(테이블\)

## Persistence

데이터를 disk에 저장하는 두가지 방법

* Snapshotting \(RDB\)
* AOF\(Append On File\)

### 1. RDB

순간적으로 메모리에 있는 내용을 DISK에 옮겨 담는 방식

1. `SAVE`
   * `blocking` 방식으로  순간적으로 redis의 모든 동작을 정지시키고, snapshot을 disk에 저장
2. `BGSAVE`
   * `non-blocking`방식으로 별도의 프로세스를 띄운 후, 명령어 수행 당시의 snapshot을 저장
   * snapshot 추출하는데 시간이 오래 걸린다 \(AWS 60GB메모리 기준 10분 소요\)

RDB작업이 실패하면 쓰기 거부 상태로 추가 장애 가능성 존재

> 참고\) RDB는 redis 서버 장애 요인의 99.9%라고 언급한 [기사](https://www.zdnet.co.kr/view/?no=20131119174125)

### 2. AOF

redis의 write/update 연산 자체를 모두 log파일에 기록하는 방식으로 RDB와 달리 특정시점이 아니라 항상 현재 시점까지의 로그를 기록하며 `non-blocking`방식이다 **모든 operation이 발생할때 마다 기록**하기 때문에 로그 데이터 양이 RDB방식에 비해서 훨씬 크며 그만금 복구시 restart속도가 느리다

## 금지

싱글 쓰레드 이므로 `keys`, `flushall`과 같은 명령어는 운영환경에 사용할 수 없다 `flushall` 명령어는 memcahce에서는 항상 일정하게 1~2ms가 나오지만 redis에서는 그 작동방식이 다르고 약 100만건의 데이터 기준으로 1000ms 정도가 나온다.

32비트 환경에서는 최대 3GB 메모리만 사용가능하지만 64비트 환경에서는 제약이 없어 OS의 가상메모리\(swap\)까지 쓰기 때문에 `redis.conf` 파일에 최대 메모리 설정을 해줘야 한다.

