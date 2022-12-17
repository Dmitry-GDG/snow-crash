[back](../../../defense.md)

<!---
//
// ###  Сделано на предыдущем шаге ###
//
//	Запустим отладчик gdb и попробуем добыть ключи из getflag:
//
//	```
//	level00@SnowCrash:~$ gdb /bin/getflag
//		GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04
//		Copyright (C) 2012 Free Software Foundation, Inc.
//		License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
//		This is free software: you are free to change and redistribute it.
//		There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
//		and "show warranty" for details.
//		This GDB was configured as "i686-linux-gnu".
//		For bug reporting instructions, please see:
//		<http://bugs.launchpad.net/gdb-linaro/>...
//		Reading symbols from /bin/getflag...(no debugging symbols found)...done.
//	(gdb) run
//		Starting program: /bin/getflag 
//		You should not reverse this
//		[Inferior 1 (process 2254) exited with code 01]
//	```
//
//	Есть защита.
//
//	Переведем программу на язык ассемблера, поищем проверку
//
//	```
//	(gdb) disas main
//	```
//
//	Видим часть кода в котором проверка
//
//	```
//	   0x08048960 <+26>:	xor    %eax,%eax	- обнуление регистра eax
//	   0x08048962 <+28>:	movl   $0x0,0x10(%esp)
//	   0x0804896a <+36>:	movl   $0x0,0xc(%esp)
//	   0x08048972 <+44>:	movl   $0x1,0x8(%esp)
//	   0x0804897a <+52>:	movl   $0x0,0x4(%esp)
//	   0x08048982 <+60>:	movl   $0x0,(%esp)
//	   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>	 - проверка ptrace, она устанавливает eax в 1
//	   0x0804898e <+72>:	test   %eax,%eax   - сравнение eax c нулем
//	   0x08048990 <+74>:	jns    0x80489a8 <main+98>
//	   0x08048992 <+76>:	movl   $0x8048fa8,(%esp)
//	   0x08048999 <+83>:	call   0x80484e0 <puts@plt>
//	  0x0804899e <+88>:	mov    $0x1,%eax
//	   0x080489a3 <+93>:	jmp    0x8048eb2 <main+1388> - переход на окончание работы программы
//	   0x080489a8 <+98>:	movl   $0x8048fc4,(%esp)
//	   ...
//	   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>	передача UID пользователя
//	   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
//	```
//
//	Обходим ptrace
//
//	```
//	(gdb) catch syscall ptrace
//		Catchpoint 1 (syscall 'ptrace' [26])
//	(gdb) commands 1
//		Type commands for breakpoint(s) 1, one per line.
//		End with a line saying just "end".
//	>set ($eax)=0
//	>continue
//	>end
//	```

//	В строке (+439) получают UID пользователя и передают в строку (+444)
-->

UID следующего пользователя получен на шаге 00.
Передадим в строчку +444 идентификатор пользователя (UID) flag07 = 3007, 
для чего изменим возвращаемое значение getuid на (UID) flag07.

<!---
// Ставим точку останова после вызова <getuid@plt>

```
// (gdb) b *main+444 - уже задана точка останова на предыдущем шаге
//	 	Note: breakpoint 2 also set at pc 0x8048b02.
//	 	Breakpoint 3 at 0x8048b02
-->

```
(gdb) r
	Catchpoint 1 (call to syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()
	Catchpoint 1 (returned from syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()
	Breakpoint 2, 0x08048b02 in main ()
(gdb) set $eax=3007
(gdb) c
	Continuing.
	Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
	[Inferior 1 (process 2620) exited normally]
(gdb) q
```

Это и есть наш флаг. 

[back](../../../defense.md)
