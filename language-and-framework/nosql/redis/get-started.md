# Get Started

설치는 [Ubuntu Simple Redis](../../../environment/ubuntu/simple-redis.md) 참조


## `Redis.conf`

```yaml
# 외부접속 허용할 경우 0.0.0.0 
bind 127.0.0.1

# 데이터베이스 수량 설정 (기본 데이터베이스는 0)
databases 16

# 주석해제후 비밀번호 설정
requirepass redis

# maxmemory <bytes>

# maxmemory-policy noeviction
```

## Syntax

> redis-server 명령어

{% code-tabs %}
{% code-tabs-item title="redis-server" %}
```bash
# redis 시작
systemctl start redis-server.service

# redis 재시작
systemctl restart redis-server.service

# 서버 재시작후 자동으로 redis 시작
systemctl enable redis-server.service
```
{% endcode-tabs-item %}
{% endcode-tabs %}

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

{% code-tabs %}
{% code-tabs-item title="key" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}