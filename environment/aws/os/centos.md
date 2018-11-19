---
description: AMI를 통한 EC2 생성 후 기본적인 개발환경 셋팅 가이드
---

# CentOS

## 1. CentOS 기본셋팅

인스턴스 생성 후 CentOS 6.X의 여러가지 설정등에 대해 설명한다.

### 1-1. 사양체크

리눅스 버전체크

```bash
uname -a
```

CentOS 버전체크

```bash
cat /etc/redhat-release
grep . /etc/*-release
```

하드용량 체크

```bash
df -h
```

메모리 체크

```bash
free -m
```

CPU 코어수 확인

```bash
cat /proc/cpuinfo | grep processor | wc -l
```

### 1-2. 시스템 시간 설정

별도의 사용자 생성 전까지는 ec2 생성시 부여받은 사용자가 아닌 root 계정으로 진행한다.

```bash
sudo -i
```

서울 리전에서 인스턴스를 생성했어도 서버시간이 UTC로 되어있으니 이를 Asia/Seoul/로 변경해준다.

```bash
vi /etc/sysconfig/clock
```

![OS_1](../../../.gitbook/assets/os_1.png)

시스템 전체에 적용하기 위해 /usr/share/zoneinfo의 적절한 파일에 /etc/localtime을 심볼릭 링크한다.

```bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

잘 적용됬는지 시간 확인

```bash
date
```

![OS_2](../../../.gitbook/assets/os_2.png)

> 추후 PHP 설치 이후 OS단 외에 Default timezone 설정이 필요하다

#### 1-3. hostname 설정

(추후 Route53 연결 후 작성)

#### 1-4. 개인 사용자 추가

사용자 추가
```bash
adduser minda
```
사용자 비밀번호 설정
```bash
passwd minda
```
웹서버 사용을 위한 권한 설정
```bash
chmod 701 /home/minda
```
사용자 전환 후 웹 디렉토리 생성
```bash
su -l minda
mkdir www
exit
```

#### 1-5. 방화벽 설정

CentOS 7 버전을 사용한다면 `firewall`을 추가 설치해서 사용해도 되지만 여기서는 6.X 버전대에서 사용가능한 `iptables` 를 사용하여 설정한다.

확인해보면 기본적으로 MarketPlace 에서 공식적으로 지원하는 AMI로 생성했을경우 기본적으로 온프레미스 환경에서 클린 설치한 CentOS와 같이 `iptables`와 `IPv6`체계에서 사용하는 `ip6tables`도 같이 설치되어 있는걸 확인할 수 있다.
```bash
rpm -qa | grep iptables
```