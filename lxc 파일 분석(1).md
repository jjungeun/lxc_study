# lxc 파일 분석( lxc_create.c )

### 

struct lxc_container  : 컨테이너의 기본 정보들(설정파일, 락, 함수들 등)

struct bdev_specs : 컨테이너의 백업 파일시스템에 대한 정보

struct lxc_log : log정보

커맨드를 파싱해서 컨테이너생성 유효성 검사를 한 후, lxc_container_new함수에 컨테이너이름과 설정경로를 인수로 넘겨 컨테이너 생성을 위한 메모리할당, 락생성 등 환경 구성.

반환된 lxc_container로 컨테이너가 기존에 있는지 검사해서 없으면,  백업 파일시스템(spec)에 컨테이너에 대한 정보를 입력.

다됐으면 lxc_container의 **create함수**로 컨테이너 생성 

create 함수가 true를 반환하면 제대로 생성된 것이므로 lxc_container_put함수의 인수로 생성된 컨테이너를 넘겨주어 thread를 감소시키고 lock을 푼다.





