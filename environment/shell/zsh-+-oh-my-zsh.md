# Zsh + Oh My Zsh

`zsh` : `bash`보다 최근에 나온 쉘 `Oh My Zsh` : `zsh`의 환결설정을 다루는 프레임워크로 커뮤니티 활동도 매우 활발하고 100여개가 넘는 플러그인가 테마들이 있다

## 1. Install

[Oh My Zsh](https://ohmyz.sh/)를 설치하려면 당연히 zsh를 먼저 설치해야 한다.

### 1-1. Ubuntu

```bash
apt-get install zsh
```

기본쉘을 zsh로 변경

```bash
# 쉘 위치 확인
$ which zsh
/usr/bin/zsh

# 기본 쉘 변경
$ chsh -s /usr/bin/zsh

# 위 두명령어 한번에 실행하기
chsh -s `which zsh`
```

터미널 재실행 혹은 ssh 재접속 후 기본쉘이 zsh로 바뀐걸 확인 후 oh my zsh 설치

```bash
# 현재 쉘 확인
$ echo $SHELL
/usr/bin/zsh

# oh my zsh 설치
$ curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh

# 스펠체크기능 활성화
$ setopt correct
```

테마 변경은 `.zshrc`파일에서 변경해준다.

```bash
vi ~/.zshrc

# 테마 변경 후 적용
source ~/.zshrc

# agnoster theme에서 사용하는 특수문자 출력 확인
echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
```

만약 변경한 테마가 깨질경우 [powerling 폰트 저장소](https://github.com/powerline/fonts)에서 폰트를 설치해준다.

{% hint style="warning" %}
powerling font는 ssh접속 프로그램에서 폰트를 적용해줘야 한다 \(GUI 환경 셋팅했으면 서버에도 설치해야됨.\)
{% endhint %}

### 2-1. Mac

모든 설정은 iterm2 기반으로 진행

```bash
brew cask install iterm2
```

zsh와 [zsh-completions](https://github.com/zsh-users/zsh-completions)를 설치합니다.

```bash
brew install zsh zsh-completions
```

zsh의 기능을 확장하고 각종 설정을 관리해주는 oh-my-zsh를 설치합니다.
이때 패스워드를 물어보는데 맞게 입력하면 스크립타가 알아서 기본쉘을 zsh로 변경해줍니다.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

oh-my-zsh를 쓰는 가장 큰 이유중에 하나인 플러그인을 설치합니다.
가장 유명한 플러그인인 Syntax-highlighting(문법강조)와 Auto Suggetions(자동완성)플러그인을 예시로 설명합니다.

```bash
# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# zsh-autosuggestions
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

플러그인을 설치했으면 zsh 설정파일인 `.zshrc` 파일에 설정을 해줘야 합니다. (안해주면 적용안됨)

```bash
plugins=(
  .. 생략
  zsh-syntax-highlighting
  zsh-autosuggestions
)
```

항상 `.zshrc` 파일을 수정했을땐 터미널을 재시작하거나 `source ~/.zshrc`명령어를 실행해서 설정파일을 다시 불러와야 적용이 됩니다.

자동완성 플러그인을 설치하면 명령어 입력도중 `tab`키를 누르면 자동완성됩니다.
그 외에 `alias`를 통해 긴 ~~ 명령어를 축약하여 사용할 수 있습니다.

```bash
# 원래 명령어
git status

# 축약 명령어
gst
```

img!

## 2. Plugin

### 2-1. Ubuntu

여러가지 플러그인들이 있지만 처음 설치하면 `.zshrc`에 `git`플러그인만 설치되어 있는데 필요한 플러그인은 설치해서 사용가능하다.

```bash
# 플러그인 설치 경로로 이동
cd ~/.oh-my-zsh/custom/plugins

# zsh-syntax-highlighting
# shell 상에서 syntax-highlighting 이 된다.
git clone https://github.com/zsh-users/zsh-syntax-highlighting

# zsh-autosuggestions
# 자동완성 기능
git clone https://github.com/zsh-users/zsh-autosuggestions
```

플러그인을 설치했으면 추가를 해준다

```bash
# 플러그인 추가
vi ~/.zshrc
```

하단의 `plugins`목록에 설치한 플러그인을 명시

```markup
plugins=(
  git
  zsh-syntax-highlighting
  zsh-autosuggestions
)
```

### 2-2. Mac

## 3. Aliases

"\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
