---
description: 성능 테스트 관련
---

# Performance Test

## 1. Linux

EC2에 접속해서 커맨드로 점검 가능한 목록

{% tabs %}
{% tab title="Bash" %}
```bash
uptimedmesg | tailvmstat 1mpstat -P ALL 1pidstat 1iostat -xz 1free -msar -n DEV 1sar -n TCP,ETCP 1top
```
{% endtab %}
{% endtabs %}

{% page-ref page="jmeter.md" %}

