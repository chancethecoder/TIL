# syslog  

Unix 운영체제 시스템의 logging을 위한 표준. 프로그램이 생성하는 메시지를 저장하도록 설계되어 있으며, 개발 환경에서는 디버깅에 활용될 수 있다. 

테스트를 위해 다음과 같이 가장 최근에 발생한 log 4줄을 출력해보자.

```bash
$ cat /var/log/syslog | tail -n 4
```

다음과 같이 현재 시스템의 로그를 출력한다.

```bash
May 25 23:56:55 vagrant-ubuntu-trusty-64 ntpdate[1969]: adjust time server 91.189.91.157 offset 0.000161 sec
May 25 23:57:13 vagrant-ubuntu-trusty-64 ntpdate[2291]: adjust time server 91.189.91.157 offset 0.006422 sec
May 25 23:57:34 vagrant-ubuntu-trusty-64 ntpdate[2302]: adjust time server 91.189.91.157 offset 0.008214 sec
May 26 00:17:01 vagrant-ubuntu-trusty-64 CRON[2883]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
```
