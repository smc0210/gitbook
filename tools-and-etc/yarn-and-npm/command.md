# Command

## Mac 환경에서 Node & NPM 업데이트

```bash
$ node -v
v5.6.0

# 강제(force) 로 캐시 삭제
sudo npm cache clean -f
npm WARN using --force I sure hope you know what you are doing.
```

`n` 모듈 설치

```bash
sudo npm i -g n
```

`n` 모듈을 사용하여 Nodejs 설치

```bash
sudo n stable
```

{% hint style="info" %}
stable 버전이 아닌 특정 버전 설치시 설치시 `sudo n {version}` 예\) `sudo n 6.1.1`
{% endhint %}

새로운 Node.js 버전 확인하기

```bash
node -v
v6.0.0
```

```bash
npm -v
3.6.0

# npm 으로 npm 설치 (인셉션..)
sudo npm i -g npm

npm -v
```

