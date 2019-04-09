# lxc 파일분석

### attach.c

실행중인 컨테이너 내의 프로세스를 실행한다. 

쉘이나 명령어를 실행하기 전, 호스트에서 pseudo터미널 마스터/슬레이브 쌍을 할당하고 터미널을 가리키고 있던 stdout 디스크립터들을 pseudo 터미널의 슬레이브로 연결한다.

lxc-attach는 pseudo 터미널 할당을 시도하지 않음에 주의해야 한다. 단지 컨테이너 네임스페이스 내부에서 쉘이나 지정한 명령어를 실행할 뿐이다.

remount_sys_proc 옵션이 있으면 /proc와 /sys를 remount하게 만든다. 현재와 다른 네임스페이스 컨텍스트를 반영하기 위함이다. 

elevated_privileges 옵션이 있으면 새로운 프로세스는 컨테이너의 cgroup에 추가되지 않는다. 

**R옵션은 attach되는 프로세스의 네트워크 /pid 네임스페이스 반영을 위해 /proc와 /sys를 다시 마운트한다. 호스트의 실제 파일 시스템으로부터 격리되기 위해 마운트 네임스페이스는 공유되지 않는다.(lxc-unshare과 비슷한 동작). /proc, /sys를 제외하고 호스트마운트 네임스페이스와 동일한 새로운 마운트 네임스페이스가 주어진다.**

표준 입출력 등이 유효 하다면 console log, uid와 gid 등을 attach 옵션에 지정하고 본격적으로 attach를 실행한다.

command에 program이 있다면 attah_run_wait을 실행하고 따로 program이 지정되어있지 않다면 attach를 실행한다.

- attach_run_wait : 컨테이너 내부의 프로그램을 실행하고 프로그램이 끝날때 까지 기다리고 나온다.

- attach : 컨테이너 내부의 프로세스를 생성하고 컨테이너 내부에서 함수를 실행한다. 그리고 lxc_wait_for_pid_status와 WIFEXITED로 해당 프로세스가 끝날 때까지 기다렸다 나온다.



