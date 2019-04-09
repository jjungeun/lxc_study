# lxc 분석

start와 execute는 똑같이 컨테이너를 실행하는 것이지만, start는 시스템컨테이너를 위해 execute는 응용 프로그램컨테이너를 위한 명령어이다.

### lxc_start.c

lxc_list를 lxc_list_init함수로 초기화를 한다.

lxc_arguments_parse로 얻은 my_args의 lxcpath를 통해 컨테이너의 위치를 얻고, 읽기전용으로 접근한다.

설정파일(rcfile)이 있다면 이를 토대로 lxc_container_new함수로 컨테이너를 생성한다. 생성할 때는 메모리를 컨테이너 구조체 크기만큼 잡고 초기화한 후 config파일대로 설정하고 난 후 본격적으로 lock을 잡는다. 설정파일이 없다면 기본 고립환경을 사용하여 컨테이너를 생성한다.

컨테이너가 정의되어 있는지 여부는 확인하지 않는다. 왜냐하면 휘발성 컨테이너의 경우 아직 생성되지 않았을 수 있기 때문이다. 단순히 config파일 구성만 통과되면 컨테이너 구조체의 start함수에 의해 컨테이너가 바로 실행된다.



### lxc_execute.c

start와 다른점을 위주로 살펴본다.

우선 execute는 lxcpath에 락을 걸고 접근하는것이 아니라 바로 접근한다. (이때는 lxc_init이 1번 pid를 갖고, 응용 프로그램이 2번 pid를 갖고 있다.) 호스트 시스템과 공유하지 않기 때문에 lock을 잡을 필요가 없는것 같다.

따로 memory를 할당하지 않는다.









