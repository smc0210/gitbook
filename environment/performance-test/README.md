---
description: 성능 테스트 관련
---

# Performance Test

## 1. Linux

EC2에 접속해서 커맨드로 점검 가능한 목록

{% code title="Bash" %}
```bash
uptime
dmesg | tail
vmstat 1
mpstat -P ALL 1
pidstat 1
iostat -xz 1
free -m
sar -n DEV 1
sar -n TCP,ETCP 1
top
```
{% endcode %}

{% page-ref page="jmeter.md" %}

