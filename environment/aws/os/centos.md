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

![OS\_1](../../../.gitbook/assets/os_1.png)

시스템 전체에 적용하기 위해 /usr/share/zoneinfo의 적절한 파일에 /etc/localtime을 심볼릭 링크한다.

```bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

잘 적용됬는지 시간 확인

```bash
date
```

![OS\_2](../../../.gitbook/assets/os_2.png)

> 추후 PHP 설치 이후 OS단 외에 Default timezone 설정이 필요하다

#### 1-3. hostname 설정

\(추후 Route53 연결 후 작성\)

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

![](../../../.gitbook/assets/os_3.png)

`CentOS`는 `Amazon Linux`, `Ubuntu` 와는 달리 기본적으로 SSH\(22\)포트만 허용이 된 상태이므로 AWS의 `Security Group` 에서 포트를 열어줬더라도 OS 단에서 추가적으로 80포트를 열여줘야 한다.

현재 열려있는 포트를 확인한다.

```bash
netstat -antl
```

![](../../../.gitbook/assets/os_4.png)

80가 닫혀있는걸 확인 할 수 있다.

또한 서비스를 확인해보면 역시 기본적으로 작동하고 있음을 확인할 수 있다.

```bash
chkconfig --list
```

> `Security Group`에서 아무리 `Inbound`설정을 해줘도 80포트를 열어주거나 방화벽 서비스를 끄지 않으면 `ELB(Elastic Load Balancer)`를 통해 외부 접근 자체가 불가능해지니 해당 설정은 매우 중요하다.

`iptables`의 설정파일의 22번 포트 셋팅을 참고하여 하단에 80포트를 추가해준다.

```bash
vi /etc/sysconfig/iptables
```

![](../../../.gitbook/assets/os_5.png)

> `iptables` **설정은 순서가 매우 중요하므로 추가적인 설정은 반드시 순서에 유념**

`table, chain, match, target, Connectio Tracking, commond` 설정은 상당이 다양하므로 여기서는 아주 간단한 규약만 명시한다.

```text
// 특정 IP 허용
-A INPUT -s ip 주소 -j ACCEPT

// 특정 IP 차단
-A INPUT -s ip 주소 -j DROP

// 특정 Port 허용
-A INPUT -p tcp –dport 443 -j ACCEPT

// 특정 Port 차단
-A INPUT -p tcp –dport 443 -j DROP
```

방화벽 설정을 끝냈다면 설정을 저장하고 방화벽을 재시작해준다.

```bash
service iptables save
service iptables restart
```

아래와 같이 나오면 정상이다.

![](../../../.gitbook/assets/os_6.png)

설정이 제대로 되었는지 룰셋을 확인한다.

```bash
iptables -nL --line-numbers
```

### 2. 패키지 설치

`yum`을 통한 패키지 설치와 기본미러링으로 제공하지 않는 패키지를 설치한다.

#### 2-1. 패키지 확인

`RedHat` 계열이므로 패키지 관리자 프로그램으로 `yum`을 사용한다.

일단 `yum`저장소 업데이트를 먼저 실행해서 패키지 목록을 갱신한다.

```bash
yum update
```

패키지가 어떤 버전대로 설치되는지 확인해본다.

```bash
yum list httpd
```

#### 2-2. 패키지 설치

`PHP`를 제외한 필요한 패키지와 개발도구들을 설치한다.

> `PHP`를 yum 패키지로 설치하지 않는 이유는 configure 설정을 사용자화 하여 직접 컴파일하기 위해서 이며, 그렇게 하지 않을경우 `configure` 설정을 임의로 수정할 수 없다.

패키지 설치하기

```bash
yum groupinstall -y 'Development Tools' \
    && yum install -y gcc \
            make \
            vim \
            wget \
            tar \
            httpd-devel \
            libxml2-devel \
            bzip2-devel \
            openssl-devel \
            curl-devel \
            gd-devel \
            libc-client-devel \
            mysql55-devel \
            aspell-devel \
            libxslt-devel \
            epel-release \
            libmcrypt-devel \
            libmhash-devel \
            mysql-devel \
            yum-fastestmirror \
            netconfig \
            ntsysv \
            wget \
            tcpdump
```

> 명령어 구분자
>
> * `;` - 앞의 명령어가 실패해도 다음 명령어가 실행
> * `&&` - 앞의 명령어가 성공했을 때 다음 명령어가 실행
> * `&` - 앞의 명령어를 백그라운드로 돌리고 동시에 뒤의 명령어를 실행

가장 중요한 아파치가 잘 설치되었는지 확인해본다.

```bash
rpm -qa | grep http
```

### 3. 아파치 환경설정

Dns name 혹은 ip로 웹에서 접근하기 위한 작업과 웹 경로를 변경해주는 작업들을 한다.

#### 3-1. 기본 설정

아파치 서비스 상태를 체크하고 꺼져있을 경우 부팅시 자동실행을 설정하고, 웹서버를 실행시킨 후 실제 웹에서 테스트 해본다.

```bash
chkconfig --list
chkconfig httpd on
service httpd start
```

![](../../../.gitbook/assets/os_7.png)

현재까지의 설정과 `Aws Management Console`에서 해당 웹서버 혹은 `elb`의 `Security Group` Inboud 설정이 사내 IP 혹은 작업환경에서의 80 포트가 열려있다면 정상적으로 apache 화면이 떠야 한다.

만약안될 경우 서비스와 방화벽을 다시한번 체크한다.

![](../../../.gitbook/assets/os_8.png)

#### 3-2. 기본 경로 변경

일반적으로 보안이슈와 관리등 여러가지 사항으로 기본으로 설정되어 있는 `/var/html` 경로를 사용하진 않으므로 경로를 바꿔준다.

방법은 여러가지가 있지만 여기서는 아파치 설정파일\(httpd.conf\)에 추가 설정파일을 링크 시키고 모든 추가적인 설정은 추가 설정파일에 작성한다.

> CentOS는 같은 RedHat 계열이더라도 아파치 설정파일 경로와 가상서버를 `enable`시키는 방식이 다르니 참고

아파치 설정 파일을 연다

```bash
vi /etc/httpd/conf/httpd.conf
```

virtualhost 설정이 적혀있는 부분을 찾아서 NameVirtualHost 주석을 해제하고 가상호스트 설정파일의 경로를 추가해준다.

```markup
NameVirtualHost *:80
include /etc/httpd/conf/extra/httpd-vhosts.conf
```

