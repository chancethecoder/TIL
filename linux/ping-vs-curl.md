# ping은 되는데 curl/wget이 안되는 경우

최근 개발 중 리눅스에서 curl/wget으로 특정 서버의 Rest API 호출 시 timeout 에러가 발생했습니다. 방화벽 문제를 의심했으나, ping은 가능한 상황입니다. 통신이 가능하므로 방화벽 외의 다른 문제가 있을 것이라고 의심하게 됐습니다.

### 결론(tl;dr)

위의 경우 **특정 port의 인바운드/아웃바운드 방화벽** 정책이 적용된 것이라고 생각할 수 있습니다.

## ping vs curl

### ping은 port를 사용하지 않는다

흔히 특정 port에 대해 통신 테스트를 할 경우 ping이 아니라 telnet을 사용합니다. ping은 port 옵션이 없기 때문입니다. 여기서 유추해볼 수 있는 것은 ping이 port를 사용하지 않는 통신 방식이라는 것입니다.

### Protocol

[curl](https://curl.haxx.se/docs/manpage.html)은 HTTP, FTP, LDAP 등 어플리케이션 계층의 다양한 프로토콜을 지원합니다. 이들은 전송 계층(TCP/UDP)에 포함되며, [well-known port](https://ko.wikipedia.org/wiki/TCP/UDP%EC%9D%98_%ED%8F%AC%ED%8A%B8_%EB%AA%A9%EB%A1%9D) 혹은 지정 port를 가지게 됩니다.

&nbsp;

그에 비해, [ping](https://linux.die.net/man/8/ping)은 ICMP 프로토콜을 사용합니다. 이는 [OSI 7 계층 모형](https://ko.wikipedia.org/wiki/OSI_%EB%AA%A8%ED%98%95)에서 네트워크 계층에 해당하며, 전송 계층보다 상위에 존재하게 됩니다. 당연히 port를 사용하지 않으며, IP 기반으로 데이터 전송 기능을 수행합니다.

&nbsp;

대표적인 라우팅 프로그램으로 traceroute(윈도우에선 tracert)가 있습니다. 이는 리눅스 기준으로 UDP 통신을 하는데, 옵션을 통해 ICMP 통신을 하도록 설정할 수 있습니다. 위 사례의 경우 traceroute 역시 통신 가능했을 것이라고 추측할 수 있겠습니다. 문제와 별개로, [기본 통신이 UDP인 이유와 차이점](https://www.linuxquestions.org/questions/linux-networking-3/why-linux-traceroute-use-udp-instead-of-icmp-815625/)은 리눅스의 권한 특성을 고려한 것으로 생각됩니다. 

## 복기

다시 현상으로 돌아가서, timeout이 발생할 경우 다음 두 가지 경우가 유력합니다.
1. request 도달 실패
2. response 도달 실패

생각해보면 결국 네트워크 문제인데, curl은 불가능하고 ping이 가능한 상황에서 두 프로그램이 같은 통신 방식을 사용할 것이라는 착각으로 인해 방화벽 문제를 배제하고 말았습니다. 평소에 아무 생각 없이 사용하는 명령어도 간혹 깊게 들어가서 살펴볼 필요가 있겠다는 생각을 했습니다.