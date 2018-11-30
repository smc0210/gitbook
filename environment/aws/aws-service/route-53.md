---
description: 'Route 53, Hosted zones, Record Set'
---

# Route 53

## TOC

* [1. Route 53](route-53.md#1-route-53)
* [2. Hosted zones](route-53.md#2-hosted-zones)
* [3. Record Set](route-53.md#3-record-set)

## 1. Route 53

AWS 리소스와 연동가능한 DNS 서비스로, 일반적인 DNS 서비스와는 다른점이 많고 보다 AWS에 특화되어 있는 서비스다.

일반적으로 사용하는 DNS 서비스 혹은 직접 구축한 DNS 서버는 도메인당 IP 주소를 한 개만 설정할 수 있고, 그래서 DNS 서버에 쿼리를 하면 항상 같은 IP 주소만 알려주지만

`Route 53`는 일반적인 DNS 서버들과는 달리 `Latency Based Routing`, `Weighted Round Robin`, `DNS Failover`, `Geo Routing`등의 기능을 제공하고, 이 기능들은 도메인 하나를 쿼리하더라도 각 상황에 따라 다른 IP 주소를 알려주는게 가능하다.

* `Latency Based Routing` : 클라이언트의 현재위치에서 가장 `지연시간(Latency)`이 낮은 리전의 IP를 알려준다.

### 일반적인 DNS 서비스 다른점

### img1

* `Weighted Round Robin` : IP주소 혹은 도메인\(`ELB`\)마다 `가중치(Weight)`를 다르게 부여하여 트래픽을 조절할 수 있게 해준다.

### img2

* `DNS Failover` : 장애가 발생한 서버의 IP주소 혹은 도메인은 알려주지 않는다.\(즉 장애가 발생해 동작하지 않는 서버로는 트래픽이 가지 않도록 해준다.\)

### img3

* `Geo Routing` 국가별, 지역별로 다른 IP 주소를 알려준다. \(같은 example.com일 경우 예시\)

### img4

* `CloudFront`, `S3`와 연동할때 `Zone Apex`\(예: www.theminda.com 대신 theminda.com\)를 지원하는데 일반적인 DNS는 CNAME으로 연결할때 루트 도메인\(theminda.com\)을 사용할 수 없다.

> **Zone Apex ?** 루트 도메인, 네이키드 도메인\(Naked Domain\)이라고도 하며, 이름 그대로 서브 도메인이 붙지 않은 상태를 뜻한다. [DNS RFC\(RFC 1033\)](https://www.ietf.org/rfc/rfc1033.txt)에 루트 도메인은 A 레코드만 지정할 수 있다고 정의되어 있다.

## 2. Hosted zones

### Hosted zone 생성

`Domain Name`에는 루트 도메인명을 입력하고 `Type`은 `Public Hosted Zone`을 선택한다.

### img5

Hosted zone을 생성하면 `NS(name server)` 와 `SOA(Start of authority)`가 자동으로 생성되고 도메인 구입처에 네임서버를 등록해준다. 간혹 네임서버 입력란이 4개 미만인 업체의 경우 **반드시 문의를 해서라도 4개의 네임서버를 모두 등록**해줘야 한다.

### img6

## 3. Record Set

`A Record`, `CNAME`, `MX` 등은 기존 DNS 서비스에서 다루던 것과 동일하지만, IP가 아닌 Alias 형식으로 지정할 경우 연결이 가능한 서비스들이 셀렉트 박스에 자동으로 노출되니 사용자는 선택만 해주면 된다. \(이를테면 ELB, CloudFront, S3등의 리소스들과 연결\)

{% hint style="warning" %}
**!주의할점!** A record 사용시 IPv6를 사용하는 리소스의 경우 IPv4, IPv6 두가지의 A Record 를 모두 지정해줘야 한다.
{% endhint %}

### Create Record Set

* Name: 생성할 서브 도메인 이름 \(예 : staging.theminda.com, dev.theminda.com \)
* Type: 레코드 종류\( A레코드, CNAME 등등..\)
* Alias: A 레코드만 사용할 수 있는 기능으로 IP 주소 대신 AWS 리소스인 S3, CloudFront, ELB를 설정할 수 있다
* TTL: Time To Live의 약자이며 A 레코드가 갱신되는 주기로 초 단위로 설정. 이후 A 레코드의 IP 주소를 바꾸면 TTL에 설정한 시간이 지나야 적용된다.
* Value: 도메인 네임을 쿼리했을 때 알려줄 IP 주소로 public IP 혹은 elastic ip를 입력해준다 \(private ip X\)
* Routing Policy: 라우팅 정책
  * Simple: 아무런 부가 기능 없이 IP 주소만 할당
  * Weighted: Weighted Round Robin 기능을 사용
  * Latency: Latency Based Routing 기능을 사용
  * Failover: DNS Failover 기능을 사용

> 참고\) `151.125.13.in-addr.arpa`는 메일발송을 위한 리버스 dns Hosted zone

