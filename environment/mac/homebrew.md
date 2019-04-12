---
description: homebrew command
---

# Homebrew

## Official site

[https://brew.sh/index\_ko](https://brew.sh/index_ko)

## Basic command

homebrew 설

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

패키지 설

```bash
brew install wget
```

```bash
cd /usr/local
$ find Cellar
Cellar/wget/1.16.1
Cellar/wget/1.16.1/bin/wget
Cellar/wget/1.16.1/share/man/man1/wget.1

$ ls -l bin
bin/wget -> ../Cellar/wget/1.16.1/bin/wget
```

{% hint style="info" %}
 Homebrew는 전용 디렉토리에 패키지를 설치하고 `/usr/local` 위치로 심볼릭 링크를 연결합니다.
{% endhint %}



