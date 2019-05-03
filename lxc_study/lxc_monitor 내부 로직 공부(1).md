# lxc_monitor 내부 로직 공부

 처음에 파싱을 하고 컨테이너 이름을 얻는다. (my_args.name = 'c1|c2')

lxcpath_cnt(lxcpath개수)와 pollfd크기만큼 malloc한다.

```
fds = malloc(my_args.lxcpath_cnt * sizeof(struct pollfd));
```

(pollfd에는 파일디스크립터, events, revents정보가 있음)

lxcpath_cnt는 기본으로 1(/var/lib/lxc)이고 따로 -P옵션으로 path를 여러개 주면 그 수만큼 할당된다.



다음의 코드를 살펴보자.

```
for (i = 0; (unsigned long)i < nfds; i++) {
    int fd;

    lxc_tool_monitord_spawn(my_args.lxcpath[i]);

    fd = lxc_monitor_open(my_args.lxcpath[i]);
    if (fd < 0) {
        close_fds(fds, i);
        goto cleanup;
    }

    fds[i].fd = fd;
    fds[i].events = POLLIN;
    fds[i].revents = 0;
}
```



### lxc_tool_monitord_spawn함수 살펴보기



**lxc_tool_monitord_spawn** 함수는 데몬 컨테이너가 시작하거나 lxc 모니터가 시작될 때 **monitord**(모니터 데몬)를 생성하는 데 사용된다.

monitord가 나갔을 때 좀비상태를 피하기 위해 2번의 fork를 한다.

(fork함수는 부모프로세스에는 자식 프로세스의 PID가, 자식 프로세스에는 0이 반환되고 실패하면 -1을 반환한다.) 

첫 번째 자식 프로세스(pid1)은 fork한 뒤 waitpid를 하여 좀비상태를 피하기 위해 기다린다. 그리고 다 끝나고 나면 retrun한다.



두번째 자식 프로세스를 fork하기 전 pipe를 생성한다.  pipefd[1]은 write하고  pipefd[0]은 read한다.



두번째 자식 프로세스(pid2)는 fork한 뒤 소켓을 생성한다. 그리고 child process와 동기화한다.

소켓을 위해 pipefd[1]을 close하고 pipefd[0]도 마찬가지로 close한다. (lxc_read_nointr필요없음)



setsid를 한다. setsid를 호출하는 프로세스가 프로세스 그룹의 리더가 아니면 새 세션을 생성해 리더가 되고 이렇게 되면 세션 아이디와 그룹아이디는 pid와 같아진다. 이후 이 프로세스에서 생성되는 모든 자식 프로세스들은 이 세션 아이디와 그룹아이디를 가지게 된다. 데몬으로 작동하는 프로그램을 위해 사용된다.



**lxc_tool_check_inherited**함수는 본격적으로 소켓을 생성하여 쓰기 전 체크한다. pid를 setting(**더 봐야할듯!!!!**)

null_stdfds()함수는 /dev/null디렉터리에서 stdin,out,err가 정상동작하는지 확인하는 기능을 한다.

모니터 데몬에 전달할 pid 인수를 생성했다면 execvp함수로 모니터 데몬을 실행한다.



### lxc_monitor_open함수 살펴보기

우선 소켓 이름과 경로를 addr.sun_path에 지정한다. 이 때 lxc/path_hash_value//lxcpath 형태로 저장된다.

```
lxc/69d2cd1be5952f52//usr/local/var/lib/lxc
```

그리고 lxc_abstract_unix_connect함수 내에서 소켓을 생성하는데, 이 소켓은 unix소켓이며 TCP통신을 하는 소켓이다.  소켓이 성공적으로 생성되었으면 addr 구조체를 초기화 하고 sun_path만 지정해 준다. 그리고 connect함수로 lxcpath의 파일에 접속 요청을 한다. 에러가 나지 않으면 fd를 반환한다.



이제 lxcpath상의 파일에 접근하여 프로세스를 연결하여 소켓을 생성하고 컨테이너의 상태를 읽어들일 준비가 되었다. 실제적으로 파일을 읽기 전에 stdout을 setlinebuf로 라인단위로 출력하게 설정한다.



실제 모니터링이 행해지는 이 부분은 2탄에서 다룬다.

```
for (;;) {
		if (lxc_monitor_read_fdset(fds, nfds, &msg, -1) < 0)
			goto close_and_clean;

		msg.name[sizeof(msg.name)-1] = '\0';
		if (regexec(&preg, msg.name, 0, NULL, 0))
			continue;

		switch (msg.type) {
		case lxc_msg_state:
			printf("'%s' changed state to [%s]\n",
			       msg.name, lxc_state2str(msg.value));
			break;
		case lxc_msg_exit_code:
			printf("'%s' exited with status [%d]\n",
			       msg.name, WEXITSTATUS(msg.value));
			break;
		default:
			/* ignore garbage */
			break;
		}
	}
```











