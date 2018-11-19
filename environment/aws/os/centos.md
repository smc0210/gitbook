---
description: AMI를 통한 EC2 생성 후 기본적인 개발환경 셋팅 가이드
---

# CentOS

### 1. CentOS 기본셋팅
인스턴스 생성 후 CentOS 6.X의 여러가지 설정등에 대해 설명한다.

#### 1-1. 사양체크

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


#### 1-2. 시스템 시간 설정

별도의 사용자 생성 전까지는 ec2 생성시 부여받은 사용자가 아닌 root 계정으로 진행한다.
```bash
sudo -i
```

서울 리전에서 인스턴스를 생성했어도 서버시간이 UTC로 되어있으니 이를 Asia/Seoul/로 변경해준다.

```bash
vi /etc/sysconfig/clock
```