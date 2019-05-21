### group을 위해 고친 부분

lxccontainers.h에 lxc_group struct

arguments.c에 lxc_arguments_parse의 g옵션추가

가장 상위 makefile에 lxc-group명령어와 헤더 파일 추가

arguments.hg에 lxc_arguments 구조체에 gname, LXC_COMMON_OPTIONS에 g 옵션 추가

lxc_group.c파일

lxc_monitor.c 그룹 옵션 부분



##### GROUP PATH설정부분

usr/src/lxc/Makefile에 LXCGROUPPATH추가

```
LXCGROUPPATH = /usr/local/var/lib/lxcgroup
```



usr/src/lxc/src/lxc/Makefile.am에 AM_CFLAGS부분에 추가

```asembly
-DLXCGROUPPATH = \"$(LXCGROUPPATH)\"\
```



usr/src/lxc/src/lxc/initutils.c에 lxcgroup폴더 생성을 위한 부분 추가

```
{ "lxc.lxcgrouppath",       NULL            },
...
char *user_lxcgroup_path = NULL
...
user_lxcgroup_path = malloc(sizeof(char) * (24 + strlen(user_home)));
...
sprintf(user_lxcgroup_path,"%s/.local/share/lxcgroup/", user_home);
...
user_lxcgroup_path = strdup(LXCGROUPPATH);
...
free(user_lxcgroup_path);
...
free(user_lxcgroup_path);
...
else if (strcmp(option_name, "lxc.lxcgrouppath") == 0) {
    remove_trailing_slashes(user_lxcgroup_path);
    values[i] = user_lxcgroup_path;
    user_lxcgroup_path = NULL;
}
...
free(user_lxcgroup_path);
```



/usr/src/lxc/configure.ac에 grouppath가 실제로 저장되는 config파일에 저장한다.

```
AC_ARG_WITH([config-grouppath],
        [AC_HELP_STRING(
            [--with-config-grouppath=dir],
            [lxcgroup configuration repository path]
            )], [], [with_config_grouppath=['${localstatedir}/lib/lxcgroup']])
```

```
AS_AC_EXPAND(LXCGROUPPATH, "$with_config_grouppath")
```

# TODO
컨테이너 삭제시 그룹에서도 삭제(lxc_destroy)
다른 path에 있는 컨테이너 그룹에 

