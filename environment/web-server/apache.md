---
description: Apache description
---

# Apache

## Snippet

Apache benchmark \(Windows\) :

```bash
 # 환경변수 등록안됬다면 풀 경로 명시
 /d/laragon/bin/apache/httpd-2.4.29-Win64-VC15/bin/ab -n 10 -c 10 http://localhost/
 
```

{% hint style="info" %}
 -n**은 요청수를**, -c **는 동시요청수를 나타낸다**.

300**명의 사용자가** 10**번씩 총** 3000**번 요청하는 것입니다**.

**사이트 주소의 끝에** / \(**슬래시**\)**를 붙여야 합니다**.
{% endhint %}



