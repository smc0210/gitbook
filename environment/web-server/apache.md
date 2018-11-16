---
description: Apache description
---

# Apache

## Snippet

### `httpd.conf`

Apache 동시접속자 확인하기

```bash
netstat -nltp |grep :80.*ESTABLISHED | wc -l
```

{% hint style="info" %}
하드웨어 자원을 고려한 가용 동시연결수 계산 = 여유 메모리 / 1연결당 메모리 2000MB free memory, 1연결당 20MB 를 사용 = 100개의 동시연결 가능. \(300~500 동시접속자 처리가능\)
{% endhint %}

### Benchmark Test

#### AB\(Apache benchmark\) - Windows

```bash
 # 환경변수 등록안됬다면 풀 경로 명시
 /d/laragon/bin/apache/httpd-2.4.29-Win64-VC15/bin/ab -n 10 -c 10 http://localhost/
```

{% hint style="info" %}
**-n**은 요청수를, **-c**는 동시요청수를 나타낸다. 즉 **-n 100 -c 300** 이라면 300명의 사용자가 총 100번을 요청한다는 뜻**. 주의사항으로는 사이트 주소의 끝에 **(/ \슬래시\)를 붙여야 한다.**
{% endhint %}

옵션은 아래와 같다

```bash
-n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make at a time
    -t timelimit    Seconds to max. to spend on benchmarking
                    This implies -n 50000
    -s timeout      Seconds to max. wait for each response
                    Default is 30 seconds
    -b windowsize   Size of TCP send/receive buffer, in bytes
    -B address      Address to bind to when making outgoing connections
    -p postfile     File containing data to POST. Remember also to set -T
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header to use for POST/PUT data, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
    -v verbosity    How much troubleshooting info to print
    -w              Print out results in HTML tables
    -i              Use HEAD instead of GET
    -x attributes   String to insert as table attributes
    -y attributes   String to insert as tr attributes
    -z attributes   String to insert as td or th attributes
    -C attribute    Add cookie, eg. 'Apache=1234'. (repeatable)
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
    -X proxy:port   Proxyserver and port number to use
    -V              Print version number and exit
    -k              Use HTTP KeepAlive feature
    -d              Do not show percentiles served table.
    -S              Do not show confidence estimators and warnings.
    -q              Do not show progress when doing more than 150 requests
    -l              Accept variable document length (use this for dynamic pages)
    -g filename     Output collected data to gnuplot format file.
    -e filename     Output CSV file with percentages served
    -r              Don't exit on socket receive errors.
    -m method       Method name
    -h              Display usage information (this message)
```

