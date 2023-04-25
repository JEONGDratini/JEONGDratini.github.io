---
date: 2023-03-13 12:00:00
layout: post
title: DevOps&#91;Linux&#93; / TIL
subtitle: 'Linux 데몬과 서비스'
description: Linux 데몬과 서비스
image: https://user-images.githubusercontent.com/62793534/234151466-da7cd7b7-c852-4241-a74e-d6660a4b68c1.png
optimized_image: https://user-images.githubusercontent.com/62793534/234151466-da7cd7b7-c852-4241-a74e-d6660a4b68c1.png
category: DevOps Bootcamp
tags:
  - linux
  - DevOps BootCamp
author: JEONGDratini

---

### 데몬? 서비스?
​
리눅스에서 데몬(Demon)은 백그라운드에서 실행되는 프로그램을 말한다. 리눅스 시스템에는 많은 데몬들이 있고, 이 데몬들은 시스템의 여러 기능을 제공하는 역할을 한다. 예를 들어서, httpd 데몬은 웹 서버 기능을 제공하고, cron 데몬은 작업 스케줄링 기능을 제공한다. 그 외에도 하드웨어 장치 관리, 마운트(USB, HDD, 등 물리적인 장치를 디렉토리에 연결해주는 작업), 프로세스간 통신, 등 많은 일들을 한다.
​
서비스(Service)는데몬의 일종으로 특정한 데몬의 실행을 관리하는 기능을 한다. 서비스를 이용해서 다른 데몬을 손쉽게 시작하거나 중지할 수 있고, 부팅 시 자동으로 실행되도록 설정할 수도 있다.
​
### 데몬 예시 : httpd
​
httpd는 Apache HTTP Server의 데몬 이름이다. Apache HTTP Server는 인터넷 상에서 웹 서버를 운영하기 위한 프로그램 중 하나로, 전 세계적으로 많이 사용되는 웹 서버 중 하나다. httpd 데몬은 백그라운드에서 클라이언트로부터 HTTP 요청을 받아들여, 이에 따른 응답을 반환한다.  
​
```
#시험용 html페이지를 만든다.
$ echo "<html><body><h1>시험용 html 페이지 <h1><body><html>" > index.html
​
#html페이지가 생성됐는지 확인한다.
$ ls
index.html
​
#웹 서버를 실행하고, 8888번 포트를 통해서 접속 할 수 있도록 한다.
$ busybox httpd -h . -p 8888
​
#웹서버에 접속해 웹 서버가 제대로 열렸는지 확인한다.
$ curl http://localhost:8888
<html><body><h1>시험용 html 페이지 <h1><body><html>
​
#프로세스 확인 명령을 해서 httpd 데몬이 실행중인지 확인한다.
$ ps aux | grep httpd
ubuntu     10876  0.0  0.0   2456    76 ?        Ss   13:25   0:00 busybox httpd -h . -p 8888
ubuntu     10884  0.0  0.2   7004  2240 pts/0    S+   13:26   0:00 grep --color=auto httpd
​
#kill 명령어를 사용해 확인한 pid(process id)로 프로세스를 종료시킨다.
$ kill -9 10876
​
#웹 서버에 다시 접속하면, 접속이 불가능 한 것을 확인할 수 있다.
ubuntu@patient-rat:~$ curl http://localhost:8888
curl: (7) Failed to connect to localhost port 8888 after 0 ms: Connection refused
```
​
### 데몬 예시 : cron
​
cron은 시스템에서 주기적으로 실행되는 작업을 관리하는 데몬이다. cron 데몬을 사용하면, 특정 시간에 특정 작업을 실행하거나, 주기적으로 반복되는 작업을 실행할 수 있다.
​
예를 들어서, 백업파일을 만드는 스크립트를 매일 자정에 실행하도록 하거나, 아래처럼 매 분마다 날짜와 시스템 가동시간을 파일에 기록하는 명령을 실행할 수 있다.
​
```
#crontab -e를 사용해 cron으로 실행할 명령을 입력할 수 있다. 여러 에디터 중 하나를 선택해서 작성할 수 있다.
$ crontab -e
no crontab for ubuntu - using an empty one
​
Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /bin/ed
​
Choose 1-4 [1]: 1
​
#에디터를 열면
# * * * * * echo $(/bin/date) - $(/usr/bin/uptime) >> ~/uptime.log
#를 입력해준다.
#이 명령은 
#'* * * * *'-> 매 분마다
#'echo $(/bin/date) - $(/usr/bin/uptime)date' -> date 명령의 출력과 uptime 명령의 출력을 합쳐서
# '>> ~/uptime.log' -> ~/uptime파일에 기록한다.
#라는 의미이다.
​
#에디터 사용이 끝나면 새 스케쥴 명령이 성공적으로 등록된 것을 확인할 수 있다.
crontab: installing new crontab
​
#시간이 지난 뒤에 uptime.log 파일을 확인해보면 작성한 예약명령이 정상적으로 작동하는 것을 확인할 수 있다.
$ cat uptime.log
Mon Mar 13 14:08:01 KST 2023 - 14:08:01 up 1 day, 23:54, 1 user, load average: 0.00, 0.00, 0.00
$ cat uptime.log
Mon Mar 13 14:08:01 KST 2023 - 14:08:01 up 1 day, 23:54, 1 user, load average: 0.00, 0.00, 0.00
Mon Mar 13 14:09:01 KST 2023 - 14:09:01 up 1 day, 23:55, 1 user, load average: 0.00, 0.00, 0.00
Mon Mar 13 14:10:01 KST 2023 - 14:10:01 up 1 day, 23:56, 1 user, load average: 0.00, 0.00, 0.00
Mon Mar 13 14:11:01 KST 2023 - 14:11:01 up 1 day, 23:57, 1 user, load average: 0.00, 0.00, 0.00
​
#가만 냅두면 uptime.log파일에 내용이 계속 쌓이게 되므로 crontab을 초기화 시켜줘야만 한다.
ubuntu@patient-rat:~$ crontab -r
ubuntu@patient-rat:~$ crontab -l
no crontab for ubuntu
```
​
-   명령을 반복할 시간을 지정해주고 싶을 때
​
앞서 입력했던 명령어의 ' \* \* \* \* \* ' 부분은 순서대로 분, 시각, 날짜(1달 기준), 달, 날짜(일주일 기준)을 말한다.
​
예를 들어서 ' 0 22 \* \* 1-5 ' 는 매주 월요일부터 금요일 22시 0분을, ' 5 4 \* \* sun' 은 매주 일요일 04시 5분을 말한다.
​
다른 예시는 [https://crontab.guru/](https://crontab.guru/)  에서 확인할 수 있다.
​
# 서비스 관리 (systemctl)
​
모든 데몬의 목록은 systemctl 명령으로 확인할 수 있다,
​
아래는 systemctl명령어의 간단한 사용 예이다.
​
```
#cron 데몬의 상태를 확인한다.
$ systemctl status -l cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2023-03-09 12:00:23 KST; 4 days ago
       Docs: man:cron(8)
   Main PID: 666 (cron)
      Tasks: 1 (limit: 1096)
     Memory: 2.5M
        CPU: 630ms
     CGroup: /system.slice/cron.service
             └─666 /usr/sbin/cron -f -P
​
Mar 13 14:08:01 patient-rat CRON[10972]: pam_unix(cron:session): session closed for user ubuntu
Mar 13 14:09:01 patient-rat CRON[10979]: pam_unix(cron:session): session opened for user ubuntu(uid=1000) by (uid=0)
Mar 13 14:09:01 patient-rat CRON[10980]: (ubuntu) CMD (echo $(/bin/date) - $(/usr/bin/uptime) >> ~/uptime.log)
Mar 13 14:09:01 patient-rat CRON[10979]: pam_unix(cron:session): session closed for user ubuntu
```
​
systemctl 명령어는 위에 있는 서비스 상태 확인 기능을 포함해서
​
-   서비스 관리 (시작, 중지, 다시 시작, 재시작, 등)
-   서비스 로그 보기
-   시스템 로그 보기
-   부팅 시 서비스 자동 실행 여부 확인
​
같은 기능들도 수행한다.
​
아래는 systemctl의 주요 하위 명령어들이다.
​
| **하위 명령어** | 설명 |
| --- | --- |
| start | 지정된 서비스를 시작한다. |
| stop | 지정된 서비스를 중지한다. |
| restart | 지정된 서비스를 다시 시작한다. |
| reload | 지정된 서비스를 다시 로드한다. |
| status | 지정된 서비스의 상태를 표시한다. |
| enable | 지정된 서비스를 부팅 시 자동으로 시작되도록 설정한다. |
| disable | 지정된 서비스를 부팅 시 자동으로 시작하지 않도록 설정한다. |
| mark | 지정된 서비스를 비활성화 하고 완전히 차단한다. |
| unmask | 지정된 서비스를 다시 활성화 한다. |
| is-enabled | 지정된 서비스가 부팅 시 자동으로 시작되는지 여부를 확인한다. |
| list-units | 현재 활성화된 모든 유닛(서비스, 타이머, 디바이스, 등)을 표시한다. |
| list-sockets | 현재 활성화된 소켓을 표시한다. |
