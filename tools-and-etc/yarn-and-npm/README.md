# Yarn & NPM

## 1. install

`NPM` 은 `Node.js` 설치시 자동으로 설치 `Yarn` 은 OS에 맞게 installer 혹은 패키지 매니저로 설치 가능하다 \(npm으로도 yarn 설치가능\)

### 1-1. macOS Install

```bash
brew install yarn# Node.js를 제외한 설치 (NVM 같은 버전관리툴 사용시)brew install yarn --without-node# upgradebrew upgrade yarn
```

### 1-2. Windows Install

[공식사이트](https://yarnpkg.com/en/docs/install#windows-stable)에서 installer를 이용해 설치하거나 다른 패키지 매니저를 통해 설치

```bash
npm install -g yarnchoco install yarnscoop install yarn
```

### 1-3. Path

전역사용이 안되면 `Path`에 등록해준다. windows 는 내PC &gt; 환경변수 &gt; Path에 등록

```bash
export PATH="$PATH:/opt/yarn-[version]/bin"
```

## 2. Usage

### 2-1. basic

```text
yarn init
```

