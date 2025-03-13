# Part II : Observe

## 1. strace

[dimitri@localhost ~]$ strace ls 2>&1 | grep write
write(1, "powerful-emotional-epic-174136.m"..., 35powerful-emotional-epic-174136.mp3

[dimitri@localhost ~]$ strace cat 2>&1 | grep open
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib64/libc.so.6", O_RDONLY|O_CLOEXEC) = 3

## 2. sysdig

[root@localhost dimitri]# sysdig proc.name=ls | grep write
2983 14:39:52.001888208 1 ls (6596) > write fd=1(<f>/dev/tty1) size=97
3008 14:39:52.002026994 1 ls (6596) < write res=97 data=Coucou  .[0m.[01;36mpowerful-emotional-epic-174136.mp3.[0m  .[01;31msysdig-0.39.

***ouverture***
2028 14:41:53.037793902 0 cat (6603) < openat fd=-2(ENOENT) dirfd=-100(AT_FDCWD) name=/usr/lib/locale/locale-archive flags=4097(O_RDONLY|O_CLOEXEC) mode=0 dev=0 ino=0

***écriture sur le terminal***
4829 14:45:05.085858913 1 cat (6619) < write res=612 data=Coucou,..Moi c'est dimitri, j'ai 38 ans et je suis un BG ! Ou pas .....

sysdig user.name=dimitri
