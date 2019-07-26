---
description: 성능 테스트 관련
---

# Performance Test

## 1. Linux

EC2에 접속해서 커맨드로 점검 가능한 목록

{% code-tabs %}
{% code-tabs-item title="Bash" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

{% tabs %}
{% tab title="First Tab" %}
1
{% endtab %}

{% tab title="Second Tab" %}
2
{% endtab %}
{% endtabs %}

