### lxc설치

git clone 해서 lxc다운로드

```
cd /usr/src
git clone https://github.com/lxc/lxc.git
```

개발도구와 의존 패키지 설치

```
apt-get install build-essential
apt-get install automake
apt-get install pkg-config
apt-get install bridge-utils
apt-get install libcap-dev
```

libcgmanger-dev를 설치하기 위해서는 apt version이 trusty여야 한다. sources.list에 다음 두줄을 추가한다.

```
vim /etc/apt/sources.list

deb http://kr.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://kr.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse
```

저장하고  apt-get update를 한다.

```
apt-get install libcgmanager-list
apt-get install lxc
apt-get install libtool
```

autogen.sh쉘 스크립트 실행(**반드시 root로**)

```
cd /usr/src/lxc
sudo autogen.sh
./configure --enable-capabilities --enable-cgmanager
```

그리고 make와  make install로 컴파일하고 바이너리, 라이브러리, 템플릿을 설치한다.

```
make
make install
```