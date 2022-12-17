[back](../../../defense.md)

Из сабжекта: 
"Then, you will be able to connect using the following couple of login:password: level00:level00."

В терминале:

```
trurgot@oa-a1 ~ % ssh level00@192.168.56.6 -p 4242
	   _____                      _____               _     
	  / ____|                    / ____|             | |    
	 | (___  _ __   _____      _| |     _ __ __ _ ___| |__  
	  \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \ 
	  ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
	 |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|
                                                        
  Good luck & Have fun

          192.168.56.6 
level00@192.168.56.6's password: level00
level00@SnowCrash:~$
```

Запустим отладчик gdb и попробуем добыть ключи из getflag:

```
level00@SnowCrash:~$ gdb /bin/getflag
	GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04
	Copyright (C) 2012 Free Software Foundation, Inc.
	License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
	This is free software: you are free to change and redistribute it.
	There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
	and "show warranty" for details.
	This GDB was configured as "i686-linux-gnu".
	For bug reporting instructions, please see:
	<http://bugs.launchpad.net/gdb-linaro/>...
	Reading symbols from /bin/getflag...(no debugging symbols found)...done.
(gdb) run
	Starting program: /bin/getflag 
	You should not reverse this
	[Inferior 1 (process 2254) exited with code 01]
```

Есть защита.

Переведем программу на язык ассемблера, поищем проверку

```
(gdb) disas main
```

Видим часть кода в котором проверка

```
   0x08048960 <+26>:	xor    %eax,%eax	- обнуление регистра eax
   0x08048962 <+28>:	movl   $0x0,0x10(%esp)
   0x0804896a <+36>:	movl   $0x0,0xc(%esp)
   0x08048972 <+44>:	movl   $0x1,0x8(%esp)
   0x0804897a <+52>:	movl   $0x0,0x4(%esp)
   0x08048982 <+60>:	movl   $0x0,(%esp)
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>	 - проверка ptrace, она устанавливает eax в 1
   0x0804898e <+72>:	test   %eax,%eax   - сравнение eax c нулем
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
   0x08048992 <+76>:	movl   $0x8048fa8,(%esp)
   0x08048999 <+83>:	call   0x80484e0 <puts@plt>
   0x0804899e <+88>:	mov    $0x1,%eax
   0x080489a3 <+93>:	jmp    0x8048eb2 <main+1388> - переход на окончание работы программы
   0x080489a8 <+98>:	movl   $0x8048fc4,(%esp)
   ...
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>	передача UID пользователя
   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
```

Обходим ptrace

```
(gdb) catch syscall ptrace
	Catchpoint 1 (syscall 'ptrace' [26])
(gdb) commands 1
	Type commands for breakpoint(s) 1, one per line.
	End with a line saying just "end".
> set ($eax) = 0
> continue
> end
```

В строке (+439) получают UID пользователя и передают в строку (+444)

Идентификаторы всех пользователей получаем с помощью команды

```
flag00@SnowCrash:~$ id flag00
	uid=3000(flag00) gid=3000(flag00) groups=3000(flag00),1001(flag)
```

Передадим в строчку +444 идентификатор пользователя flag00, 
для чего изменим возвращаемое значение getuid на uid flag00.
Ставим точку останова после вызова <getuid@plt>

```
(gdb) b *main+444
	Breakpoint 2 at 0x8048b02
(gdb) r
	Catchpoint 1 (call to syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()
	Catchpoint 1 (returned from syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()
	Breakpoint 2, 0x08048b02 in main ()
(gdb) set $eax=3000
(gdb) c
	Continuing.
	Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
(gdb) q
```

Это и есть наш флаг.

Получим UID остальных пользователей:

```
flag00@SnowCrash:~$ id flag00
	uid=3000(flag00) gid=3000(flag00) groups=3000(flag00),1001(flag)
flag00@SnowCrash:~$ id flag01
	uid=3001(flag01) gid=3001(flag01) groups=3001(flag01),1001(flag)
flag00@SnowCrash:~$ id flag02
	uid=3002(flag02) gid=3002(flag02) groups=3002(flag02),1001(flag)
flag00@SnowCrash:~$ id flag03
	uid=3003(flag03) gid=3003(flag03) groups=3003(flag03),1001(flag)
flag00@SnowCrash:~$ id flag04
	uid=3004(flag04) gid=3004(flag04) groups=3004(flag04),1001(flag)
flag00@SnowCrash:~$ id flag05
	uid=3005(flag05) gid=3005(flag05) groups=3005(flag05),1001(flag)
flag00@SnowCrash:~$ id flag06
	uid=3006(flag06) gid=3006(flag06) groups=3006(flag06),1001(flag)
flag00@SnowCrash:~$ id flag07
	uid=3007(flag07) gid=3007(flag07) groups=3007(flag07),1001(flag)
flag00@SnowCrash:~$ id flag08
	uid=3008(flag08) gid=3008(flag08) groups=3008(flag08),1001(flag)
flag00@SnowCrash:~$ id flag09
	uid=3009(flag09) gid=3009(flag09) groups=3009(flag09),1001(flag)
flag00@SnowCrash:~$ id flag10
	uid=3010(flag10) gid=3010(flag10) groups=3010(flag10),1001(flag)
flag00@SnowCrash:~$ id flag11
	uid=3011(flag11) gid=3011(flag11) groups=3011(flag11),1001(flag)
flag00@SnowCrash:~$ id flag12
	uid=3012(flag12) gid=3012(flag12) groups=3012(flag12),1001(flag)
flag00@SnowCrash:~$ id flag13
	uid=3013(flag13) gid=3013(flag13) groups=3013(flag13),1001(flag)
flag00@SnowCrash:~$ id flag14
	uid=3014(flag14) gid=3014(flag14) groups=3014(flag14),1001(flag)
flag00@SnowCrash:~$ id flag15
	id: flag15: No such user
```

[back](../../../defense.md)
