---
description: homebrew command
---

# Homebrew

## Official site

[https://brew.sh/index\_ko](https://brew.sh/index_ko)

## Basic command

homebrew 설치

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

패키지 설치

```bash
brew install wget
```

권한문제로 link 설정 안될 경우 아래와 같이 재설치

```bash
# Output your installed packages (via brew) to a text file:brew list > brewlist.txt# Uninstall brew:ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"# Re-install brew:/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"#Re-install previous packages (edit list if you do not need all packages):brew install $(< brewlist.txt )
```

```bash
cd /usr/local$ find CellarCellar/wget/1.16.1Cellar/wget/1.16.1/bin/wgetCellar/wget/1.16.1/share/man/man1/wget.1$ ls -l binbin/wget -> ../Cellar/wget/1.16.1/bin/wget
```

{% hint style="info" %}
Homebrew는 전용 디렉토리에 패키지를 설치하고 `/usr/local` 위치로 심볼릭 링크를 연결합니다.
{% endhint %}

