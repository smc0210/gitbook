# Node.js

## 1. Install

윈도우 환경과 MacOS는 공식홈페이지에서 설치파일 받아서 그냥 설치하면 끝
(물론 맥은 직접 설치 권장)

Ubuntu 에서는 최신버전을 설치하려면 Nodesource에서 배포하는 저장소를 추가해줘야 한다.
더 자세한 사항은 공식홈 가이드 참조 [nodejs 공식홈 - 번역](https://nodejs.org/ko/download/package-manager/)

```bash
# 작성일 기준 LTS 버전인 8.x 대로 설치..
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
apt-get install -y nodejs

# 만약 10.x 설치하려면 
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -

# 설치 확인
$ node -v
v8.11.4

$ npm -v
5.6.0
```

