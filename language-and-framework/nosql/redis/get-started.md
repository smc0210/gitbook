# Get Started

설치는 [Ubuntu Simple Redis](../../../environment/ubuntu/simple-redis.md) 참조

## Client Tool

redis client Tool은 여러가지를 사용해본 결과 [luin/medis](https://github.com/luin/medis)가 사용하기에 적절하다.

데이터 확인과 cli 명령뿐만 아니라 제어 가능한 모든 redis 설정까지 확인 및 수정이 가능하다.

> 그 외에는 유료이거나, 기능이 너무 적다.

Appstore에서 다운받으면 4.99$ 지만 직접 빌드하여 사용하면 무료이고 react와 eletron으로 만들어졌다.

## `Redis.conf`

```yaml
# 외부접속 허용할 경우 0.0.0.0 
bind 127.0.0.1

# 데이터베이스 수량 설정 (기본 데이터베이스는 0)
databases 16

# 주석해제후 비밀번호 설정
requirepass redis

# redis 최대 메모리 설정
# 0으로 설정시 메모리 제한 없음
# maxmemory <bytes>

# redis 최대 매모리에 도달했을 경우 정책
# maxmemory-policy allkeys-lru
```

| policy | description |
| :--- | :--- |
| noeviction | 기존 데이터 삭제 안함. 메모리 한계에 도달하면 OOM 오류 반환. 새 데이터 저장되지 않음 |
| allkeys-lru | LRU로 삭제하여 공간확보하고 새 데이터 저장 |
| volatile-lru | expire set을 가진 것 중 LRU로 삭제하여 공간확보하고 새 데이터 저장 |
| allkeys-random | 랜덤으로 삭제하여 공간확보하고 새 데이터 저장 |
| volatile-random | expire set을 가진 것 중에서 랜덤으로 삭제하여 공간확보하고 새 데이터 저장 |
| volatile-ttl | expire set을 가진 것 중 TTL이 짧은 것부터 삭제하여 공간확보하고 새 데이터 저장 |

* 데이터 보존 용도로는 `noeviction`
* 캐시 용도로는 `allkeys-lru`를 사용
* `volatile-` 시리즈는, 메모리 부족한데 expire 대상 없으면 OOM 오류 반환

## Syntax

> redis-server 명령어

{% code title="redis-server" %}
```bash
# redis 시작
systemctl start redis-server.service

# redis 재시작
systemctl restart redis-server.service

# 서버 재시작후 자동으로 redis 시작
systemctl enable redis-server.service
```
{% endcode %}

> 기본 명령어

```bash
명령어 도움말
help COMMAND

# 저장된 키 정보 출력
info keyspace

# db 이동 (기본값은 0~16 사이)
select DBNUMBER

# 모든 key 삭제 ( 운영환경에서 절대 금지 - memcache와 작동방식 다름)
flushall
```

> `key` 관련

{% code title="key" %}
```bash
# 해당 키의 값 출력
get KEY

# value의 길이 출력
strlen KEY

# 모든 키 출력 ( 운영환경에서 절대 금지)
keys * 

# s로 시작하는 모든 키 검색
keys s*

# n으로 끝나는 모든 키 검색
keys *n

# 특정 패턴에 일치되는 키 출력
keys pattern

# 지정된 키의 이름을 변경
rename OLDKEY NEWKEY

# 키 삭제
del KEY

# 키 존재 유무 검색
exists KEY

# 키 타임아웃 지정 (지정된 시간 후에 자동으로 삭제)
expire KEY SECONDS

# 해당 db로 키 이동
move KEY DBNUMBER

# 해당 키의 타입 출력
type KEY
```
{% endcode %}

