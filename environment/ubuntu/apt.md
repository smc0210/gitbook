---
description: apt-get(Advanced Packaging Tool) Command
---

# Apt

 `apt-get(Advanced Packaging Tool)`은 `Ubuntu`를 포함한 `Debian` 계열의 리눅스에서 쓰이는 패키지 관리 명령어 도구

 [yum 과 명령어 비교 사이트](https://help.ubuntu.com/community/SwitchingToUbuntu/FromLinux/RedHatEnterpriseLinuxAndFedora)
 
{% hint style="info" %}
Ubuntu 14 이상은 apt 명령어를 더 간단하게 사용가능
{% endhint %}

```bash
# 등록된 저장소내 전체 패키지 인덱스 업데이트
apt-get update

# 전체 패키지 업데이트
apt-get upgrade

# 의존성을 검사하며 업데이트
apt-get dist-upgrade

# 패키지 설치
apt-get install package-name
apt-get install gcc

# 패키지 재설치
apt-get --reinstall install package-name
apt-get --reinstall install gcc

# 패키지만 삭제
# 설정파일을 지우지 않고 패키지만 삭제
apt-get remove package-name
apt-get remove gcc

# 설정파일까지 삭제
apt-get purge  package-name
apt-get purge gcc

# 패키지 검색
apt-cache search package-name
apt-cache search php7

# 확장검색
dpkg -l *package-name*

# 설치된 전체 패키지 조회
dpkg --get-selections | grep -v deinstall

# 설치된 전체 패키지 조회 - Ubuntu 14 이상
apt list --installed

# 특정 패키지 설치 여부 확인
dpkg --get-selections | grep package-name
dpkg --get-selections | grep postgres

# 패키지 정보 보기
apt-cache show package-name
apt-cache show php5-fpm

# 파일이 속한 패키지 찾기
apt-file search
apt-file search
```


{% hint style="info" %}
apt를 이용해서 설치된 deb패키지는 `/var/cache/apt/archive/` 에 설치됨
{% endhint %}

