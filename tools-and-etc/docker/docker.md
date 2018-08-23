# Get Started

한국어 번역\(초벌\) : [nacyot](http://nacyot.com)

## 왜 Docker를 사용해야하는가?

[Why Should I Care \(For Developers\)](https://www.docker.io/the_whole_story/#Why-Should-I-Care-%28For-Developers%29)

{% hint style="info" %}
"나에게 Docker의 매력은 간단히 격리된 환경을 만들 수 있다는 것과, 그러한 환경을 재사용할 수 있다는 점이다."런타임 환경을 한 번 만들어 패키지로 만들면, 이 패키지를 다른 어떤 머신에서도 다시 사용할 수 있다. 또한 여기서 실행되는 모든 것은 마치 가상머신과 같이 호스트로부터 격리되어있다. 무엇보다도 이런 모든 일들이 빠르고 간단히 가능하다.
{% endhint %}

## 지금 바로 Docker 개발 환경 구축하기

* [24시간 동안 Docker 개발 한경 구축하기\(A Docker Dev Environment in 24 Hours!\)](http://blog.relateiq.com/a-docker-dev-environment-in-24-hours-part-2-of-2/)
* [\(Docker와 함께 개발환경 구축하기\)Building a Development Environment With Docker](http://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Docker 컨테이너에 대한 이야기\(Discourse in a Docker Container\)](http://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## 필요한 것들

[Homebrew](http://brew.sh/) 설치.

```text
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

## 설치

[Brew Cask](https://github.com/phinze/homebrew-cask)를 사용해 VirtualBox와 Vagrant 설치.

```text
brew tap phinze/homebrew-cask
brew install brew-cask
brew cask install virtualbox
brew cask install vagrant
```

여기서는 미리 준비한 vagrant 박스를 사용한다: [http://blog.phusion.nl/2013/11/08/docker-friendly-vagrant-boxes/](http://blog.phusion.nl/2013/11/08/docker-friendly-vagrant-boxes/)

```text
mkdir mydockerbox
cd mydockerbox
vagrant init docker https://oss-binaries.phusionpassenger.com/vagrant/boxes/ubuntu-12.04.3-amd64-vbox.box
vagrant up
vagrant ssh
```

가상 머신 내부에서:

```text
sudo su -
sh -c "curl https://get.docker.io/gpg | apt-key add -"
sh -c "echo deb http://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
apt-get update
apt-get install -y lxc-docker
```

제대로 작동하는 지 확인:

```text
docker run -i -t ubuntu /bin/bash
```

설치는 이게 전부다. 마지막 명령어를 통해 Docker 컨테이너 하나가 실행되었다.

## 컨테이너

[Your basic isolated Docker process](http://docker.readthedocs.org/en/latest/terms/container/#container-def). 컨테이너와 가상 머신 간의 관계는 쓰레드와 프로세스 간의 관계와 비슷하다. 아니면 스테로이드 주사를 맞은 chroot라고도 생각할 수 있다.

몇 가지 오해가 있다면:

* **컨테이너는 일시적으로 작동하지 않는다**.  `docker run`은 당신이 얼핏 예상하는 그런 식으로 작동하지 않는다.
* **컨테이너에서 오직 하나의 명령어나 커맨드만 실행시킨다는 것은 잘못된 생각이다.** [supervisord](http://docs.docker.io/en/latest/examples/using_supervisord/)나 [runit](https://github.com/phusion/baseimage-docker)를 사용할 수 있다..

### 라이프 사이클

* [`docker run`](http://docs.docker.io/en/latest/commandline/cli/#run) 컨테이너를 생성한다.
* [`docker stop`](http://docs.docker.io/en/latest/commandline/cli/#stop) 컨테이너를 정지시킨다.
* [`docker start`](http://docs.docker.io/en/latest/commandline/cli/#start) 컨테이너를 다시 실행시킨다. 
* [`docker restart`](http://docs.docker.io/en/latest/commandline/cli/#restart) 컨테이너를 재가동한다.
* [`docker rm`](http://docs.docker.io/en/latest/commandline/cli/#rm) 컨테이너를 삭제한다.
* [`docker kill`](http://docs.docker.io/en/latest/commandline/cli/#kill) 컨테이너에게 SIGKILL을 보낸다.  [이에 관련된 이슈가 있다.](https://github.com/dotcloud/docker/issues/197).
* [`docker attach`](http://docs.docker.io/en/latest/commandline/cli/#attach) 실행중인 컨테이너에 접속한다. \* [`docker wait`](http://docs.docker.io/en/latest/commandline/cli/#wait) 컨테이너가 멈출 때까지 블럭한다.

컨테이너를 실행하고 컨테이너에 접속하고자 할 때는 `docker start` 명령어를 실행하고 `docker attach` 명령어를 실행한다.

일시적인 컨테이너를 생성하고자 할 때는 `docker run -rm` 명렁어를 사용해 컨테이너를 생성할 수 있다. 이 컨테이너는 멈춰지면 삭제된다.

이미지 안을 뒤질 필요가 있을 때는 `docker run -t -i <myshell>` 명령어로 tty를 열 수 있다.

호스트의 디렉토리와 Docker 컨테이너 디렉토리를 맵핑하고자 할 때는 `docker run -v $HOSTDIR:$DOCKERDIR` 명령어를 사용할 수 있다\(also see Volumes section\).

컨테이너를 [host process manager](http://docs.docker.io/en/latest/use/host_integration/)와 통합하고자 할 때는 Dockre 데몬을`-r=false` 옵션으로 실행시키고 `docker start -a` 명령어를 실행하면 된다.

컨테이너의 포트를 호스트 쪽으로 열어 주고자 할 때는 [exposing ports](https://gist.github.com/wsargent/7049221#exposing-ports)을 참조하면 된다.

### 관련된 정보를 출력해주는 명령어

* [`docker ps`](http://docs.docker.io/en/latest/commandline/cli/#ps) 명령어는 실행중인 컨테이너 목록을 보여준다.
* [`docker inspect`](http://docs.docker.io/en/latest/commandline/cli/#inspect) ip 주소를 포함한 특정 컨테이너에 대한 모든 정보를 보여준다.
* [`docker logs`](http://docs.docker.io/en/latest/commandline/cli/#logs) 컨테이너로부터 로그를 가져온다.
* [`docker events`](http://docs.docker.io/en/latest/commandline/cli/#events) 컨테이너로부터 이벤트를 가져온다.
* [`docker port`](http://docs.docker.io/en/latest/commandline/cli/#port) 컨테이너의 특정 포트가 어디로 연결되어있는지 보여준다.
* [`docker top`](http://docs.docker.io/en/latest/commandline/cli/#top) 컨테이너에서 실행중인 프로세스를 보여준다.
* [`docker diff`](http://docs.docker.io/en/latest/commandline/cli/#diff) 컨테이너 파일 시스템에서 변경된 파일들을 보여준다.

`docker ps -a` 실행중인 컨테이너와 멈춰있는 컨테이너를 모두 보여준다.

### Import / Export

* [`docker cp`](http://docs.docker.io/en/latest/commandline/cli/#cp) 컨테이너 내의 파일을 호스트로 복사한다.
* [`docker export`](http://docs.docker.io/en/latest/commandline/cli/#export) 컨테이너 파일 시스템을 tarball로 출력한다.

## Docker 이미지

이미지는 그저 [Docker 컨테이너의 템플릿](http://docker.readthedocs.org/en/latest/terms/image/)이다.

### 라이프 사이클

* [`docker images`](http://docs.docker.io/en/latest/commandline/cli/#images) 모든 이미지 목록을 보여준다.
* [`docker import`](http://docs.docker.io/en/latest/commandline/cli/#import) tarball 파일로부터 이미지를 생성한다.
* [`docker build`](http://docs.docker.io/en/latest/commandline/cli/#build) Dockerfile을 통해 이미지를 생성한다.
* [`docker commit`](http://docs.docker.io/en/latest/commandline/cli/#commit) 컨테이너에서 이미지를 생성한다.
* [`docker rmi`](http://docs.docker.io/en/latest/commandline/cli/#rmi) 이미지를 삭제한다.
* [`docker insert`](http://docs.docker.io/en/latest/commandline/cli/#insert) URL에서 이미지로 파일을 집어넣는다. \* [`docker load`](http://docs.docker.io/en/latest/commandline/cli/#load) 표준 입력으로 tar 파일에서 \(이미지와 태그를 포함한\) 이미지를 불러온다.\(0.7부터 사용가능\).
* [`docker save`](http://docs.docker.io/en/latest/commandline/cli/#save) 모든 부모 레이어와 태그, 버전 정보를 tar 형식으로 표준출력을 통해  @@@ \(0.7부터 사용가능\).

`docker import`와 `docker commit` 파일 시스템만 셋업하고 Dockefile과 같은 CMD, ENTRYPOINT, EXPOSE는 포함하지 않는다.. [bug](https://github.com/dotcloud/docker/issues/1141) 참조.

### 관련된 정보를 출력해주는 명령어

* [`docker history`](http://docs.docker.io/en/latest/commandline/cli/#history) 이미지의 이력 정보를 보여준다.
* [`docker tag`](http://docs.docker.io/en/latest/commandline/cli/#tag) 이미지에 이름으로 태그를 붙여준다\(local 혹은 registry\).

## 레지스트리\(Registry\) & 저장소\(Repository\)

저장소\(repository\)란 컨테이너를 위한 파일 시스템을 생성할 수 있는 호스트되는 태그가 붙어있는 이미지들의 집합.

레지스트리란 저장소를 저장해두고 HTTP API를 통해 [저장소의 업로드, 관리, 다운로드](docker.md)를 제공하는 호스트를 의미한다. @@@

Docker.io는 매우 다양한 저장소를 포함하고 있는 이미지 \[index\]를 가지고 있는 중앙 레지스트리이다. @@@

* [`docker login`](docker.md) 레지스트리에 로그인한다.
* [`docker search`](http://docs.docker.io/en/latest/commandline/cli/#search) 레지스트리에서 이미지를 검색한다.
* [`docker pull`](http://docs.docker.io/en/latest/commandline/cli/#pull) 이미지를 레지스트리에서 로컬 머신으로 가져온다\(pull\).
* [`docker push`](http://docs.docker.io/en/latest/commandline/cli/#push) 이미지를 로컬 머신에서 레지스트리에 집어넣는다\(push\).

## Dockerfile

[Docker 설정 파일](http://docs.docker.io/en/latest/use/builder/#dockerbuilder). `docker build` 명령어를 통해서 Docker 컨테이너를 만들어낸다. 또한 `docker commit` 명령어보다 추천되는 명령어이다.

* [Dockerfile tutorial, level 1](http://www.docker.io/learn/dockerfile/level1/)
* [Dockerfile tutorial, level 2](http://www.docker.io/learn/dockerfile/level2/)

[http://github.com/wsargent/docker-devenv](http://github.com/wsargent/docker-devenv)를 추천한다. 더 자세한 내용은 [best practices](http://crosbymichael.com/dockerfile-best-practices.html) 이 글을 추천한다.

## 레이어

Docker에서 [버전화된 파일 시스템은](http://en.wikipedia.org/wiki/Aufs) 레이어에 기반하고 있다.. 이는 [파일 시스템을 위한 git commits나 차분](http://docker.readthedocs.org/en/latest/terms/layer/)이라고 생각할 수 있다.

## 링크

Docker 컨테이너들이 [TCP/IP ports](http://docs.docker.io/en/latest/use/working_with_links_names/)으로 링크를 통해 서로 정보를 주고받는다.. [Redis로 링크 걸기](http://docs.docker.io/en/latest/examples/linking_into_redis/) and [Atlassian](http://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/)에서는 어떻게 작동되는지 예제를 보여준다.

참고 : 만약 컨테이너들 간에 오직 링크를 통해서만 통신을 허용하고 싶다면 Docker 데몬을 실행할 때 `icc=false` 옵션을 통해 프로세스간 통신을 막을 수 있다.

예를 들어 CONTAINER이라는 이름을 가진 컨테이너가 있다고 하자\(`docker run -name CONTAINER`\). 이 컨테이너는 Dockerfile에서 port를 아래와 같이 노출시킨다.

```text
EXPOSE 1337
```

그리고 다음과 같이 LINKED라고 이름붙여진 또 다른 컨테이너를 만든다.

```text
docker run -d -link CONTAINER:ALIAS -name LINKED user/wordpress
```

노출된 포트와 CONTAINER의 별칭이 아래와 같이 LINKED 컨테이너의 환경변수로 나타난다:

```text
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

이를 통해 연결할 수 있다.

이 링크를 삭제하려면 `docker rm -link` 명령어를 사용하면 된다.

## 볼륨

Docker의 볼륨은 [유동적인 파일시스템](http://docs.docker.io/en/latest/use/working_with_volumes/)이다. 볼륨은 특정 컨테이너에 연결되어있지 않아도 된다.

볼륨은 TCP/IP를 통한 링크가 사용가능한 환경에서 유용하다. 예를 들어 두 Docker 인스턴스에서 파일 시스템으로부터 떨어져서 통신을 해야하는 경우가 있다.

먼저 이것들을 docker 컨테이너에 동시에 마운트 하고 `docker run -volume-from` 명령어를 실행합니다.

더 자세한 사항은 [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) 페이지를 참고해주세요.

## 포트 노출하기

포트를 노출시키는 일은 [귀찮지만 유용하다](http://docs.docker.io/en/latest/use/port_redirection/#binding-a-port-to-an-host-interface).

먼저 Dockerfile에서 포트를 노출시킨다.

```text
EXPOSE <CONTAINERPORT>
```

그리고 `docker run` 명령어를 통해서 컨테이너의 포트와 호스트의 포트를 맵핑시킨다.

```text
docker run -p $HOSTPORT:$CONTAINERPORT -name CONTAINER -t someimage
```

Docker가 가상 머신에서 실행중이라면 추가적인 포트포워딩을 해줘야한다. 이러한 설정을 할 때는 Vagrantfile을 통해서 특정 범위의 포트를 노출시켜 동적으로 맵핑하는 게 편리하다.

```text
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

만약 포트가 어디로 연결되었는지 기억이 안 난다면 `docker port` 명령어를 사용할 수 있다.

```text
docker port CONTAINER $CONTAINERPORT
```

## 팁

참고:

* [15 Docker Tips in 5 minutes](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)

### 마지막에 실행된 컨테이너의 ID

```text
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit `dl` helloworld
```

### 명령어와 함께 커밋하기

```text
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' `dl` postgres
```

### IP address 정보

```text
docker run -i -t ubuntu /bin/bash
```

명령어를 사용하거나 아래 명령어를 사용합니다.

```text
wget http://stedolan.github.io/jq/download/source/jq-1.3.tar.gz
tar xzvf jq-1.3.tar.gz
cd jq-1.3
./configure && make && sudo make install
docker inspect `dl` | jq -r '.[0].NetworkSettings.IPAddress'
```

### 이미지의 환경 변수 읽어오기

```text
docker run -rm ubuntu env
```

### 오래된 컨테이너들 삭제하기

```text
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### 멈춰있는 컨테이너들 삭제하기

```text
docker rm `docker ps -a -q`
```

### 이미지의 의존관계 이미지로 출력하기

```text
docker images -viz | dot -Tpng -o docker.png
```

