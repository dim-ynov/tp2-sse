# TP2 : Syscalls

## 1. Anatomy of a program

### A. `file`

[dimitri@localhost ~]$ file /bin/ls
/bin/ls: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1afdd52081d4b8b631f2986e26e69e0b275e159c, for GNU/Linux 3.2.0, stripped

[dimitri@localhost ~]$ file /usr/sbin/ip
/usr/sbin/ip: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=77a2f5899f0529f27d87bb29c6b84c535739e1c7, for GNU/Linux 3.2.0, stripped

[dimitri@localhost ~]$ file /home/dimitri/powerful-emotional-epic-174136.mp3
/home/dimitri/powerful-emotional-epic-174136.mp3: MPEG ADTS, layer III, v1, 256 kbps, 44.1 kHz, JntStereo

### B. `readelf`

[dimitri@localhost ~]$ readelf -h /bin/ls
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x6b10
  Start of program headers:          64 (bytes into file)
  Start of section headers:          139032 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         30
  Section header string table index: 29

[15] .text             PROGBITS         0000000000004d50  00004d50
       0000000000012532  0000000000000000  AX       0     0     16


### C. `ldd`

[dimitri@localhost ~]$ ldd /bin/ls
        linux-vdso.so.1 (0x00007ffd59784000)
        libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fbcf5fab000)
        libcap.so.2 => /lib64/libcap.so.2 (0x00007fbcf5fa1000)
        libc.so.6 => /lib64/libc.so.6 (0x00007fbcf5c00000)
        libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fbcf5f05000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fbcf6002000)

libc.so.6 => /lib64/libc.so.6 (0x00007fbcf5c00000)

## 2. Syscalls basics

### A. Syscall list

- lire un fichier stocké sur disque :
Nom : read ; Identifiant unique : 0
- écrire dans un fichier stocké sur disque :
Nom : write ; Identifiant unique : 1
- lancer un nouveau processus :
Nom : fork, clone ; Identifiant unique : 57, 56

### B. `objdump`

**Utiliser `objdump`sur la commande `ls`**

handler@@Base+0x26c>
   1724f:       48 89 c1                mov    %rax,%rcx
   17252:       0f 85 5f fe ff ff       jne    170b7 <_obstack_memory_used@@Base+0x67a7>
   17258:       e9 60 fe ff ff          jmpq   170bd <_obstack_memory_used@@Base+0x67ad>
   1725d:       e8 4e d6 fe ff          callq  48b0 <__stack_chk_fail@plt>
   17262:       66 2e 0f 1f 84 00 00    nopw   %cs:0x0(%rax,%rax,1)
   17269:       00 00 00
   1726c:       0f 1f 40 00             nopl   0x0(%rax)
   17270:       f3 0f 1e fa             endbr64
   17274:       48 8b 15 05 9d 00 00    mov    0x9d05(%rip),%rdx        # 20f80 <_obstack_memory_used@@Base+0x10670>
   1727b:       31 f6                   xor    %esi,%esi
   1727d:       e9 ae d9 fe ff          jmpq   4c30 <__cxa_atexit@plt>

0000000000000000      DO *UND*  0000000000000000  GLIBC_2.2.5 program_invocation_short_name
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.2.5 iswprint
0000000000000000  w   DF *UND*  0000000000000000  GLIBC_2.2.5 __cxa_finalize
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.2.5 sigaddset
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.3   __ctype_tolower_loc
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.3   __ctype_b_loc
0000000000000000      DO *UND*  0000000000000000  GLIBC_2.2.5 stderr
0000000000000000      DF *UND*  0000000000000000  GLIBC_2.3.4 __sprintf_chk

[dimitri@localhost ~]$ objdump -d -j .text /bin/ls | grep syscall
[dimitri@localhost ~]$ 

**Utiliser `objdump`sur la librairie Glibc**


[dimitri@localhost ~]$ objdump -d /lib64/libc.so.6 | grep -E "syscall"
   295f4:       0f 05                   syscall
   3e737:       0f 05                   syscall
   3e801:       0f 05                   syscall
   3e969:       0f 05                   syscall
   3e99e:       0f 05                   syscall
   3e9ea:       0f 05                   syscall
   3ea18:       0f 05                   syscall
   3ef49:       0f 05                   syscall

     127955:       b8 03 00 00 00          mov    $0x3,%eax
  12795a:       8b 7d a0                mov    -0x60(%rbp),%edi
  12795d:       0f 05                   syscall