# IP 주소의 기본

IP란 송신/수신 클라이언트에서 정보를 주고 받을 때 사용하는 정보 위주의 프로토콜을 말한다. 쉽게 말하자면 `인터넷에서 호스트의 주소`를 말한다. IP의 역할은 `패킷`이라는 통신 단위로 지정한 IP 주소에 데이터를 전달하는 것을 목적으로 한다. 여기서 간단히 서브넷, 허브, 라우터에 대한 개념을 짚고 넘어가야된다. 

> 💡 <br>
> **서브넷** 
> 허브에 몇 대의 컴퓨터가 접속한 것인지의 한 개의 단위<br>
> **라우터**
> 서브넷들을 연결해준다

## 리퀘스트 메시지 전송 단계

![image](https://github.com/user-attachments/assets/0be80058-c508-4460-a5f3-ba6293abdfba)

1. 송신측이 메시지를 보내면 서브넷 안에 있는 허브가 메시지를 운반한다. 
2. 송신측에서 가장 가까운 라우터에 도착한다. 
3. 라우터가 메시지를 보고 다음 라우터로 보낸다. 
4. 메시지를 보낸 뒤 해당 서브넷의 허브가 다시 메시지를 운반한다. 

# 도메인명과 IP 주소를 구분하여 사용하는 이유

![image](https://github.com/user-attachments/assets/65043b7a-bba7-4054-aef3-070ef92d8b2f)


ping 테스트를 통해 naver.com의 IP는 223.130.192.248인 것을 알 수 있다. 

TCP/IP 네트워크는 IP 주소로 통신상대를 지정하며 IP 주소를 모르면 상대방에게 메시지를 전달할 수 없다. 그러나 모든 웹사이트의 IP 주소를 외우고 있는 것은 매우 번거로운 일이다. 마치 전화번호부의 전화번호를 모두 외우고 있는 것과 같다. 따라서 URL 안의 IP 주소 대신 서버의 이름을 사용하기로 했고, 이를 `도메인`이라고 한다. 전화번호부에 이름과 전화번호를 대응시키는 것처럼, IP 주소와 도메인을 대응시키는 구조를 `DNS`(Domain Name System) 이라고 한다. 

> 💡 <br>
> **그럼 처음부터 IP가 아니라 도메인으로 쓰면 되는거 아냐?**<br>
> 비트 주소 대신 문자열을 쓴다면 전자는 최대 32바이트이지만 후자는 255바이트까지 가능하다. <br>
> 이는 즉 라우터의 부하를 의미하고 네트워크 속도의 저하로 이어진다. <br>

# Socket 라이브러리가 IP 주소를 찾는 기능을 제공한다

브라우저가 DNS를 통해 도메인 주소를 IP로 변환하는 것은 알겠는데, 브라우저에서 DNS에는 어떻게 접근하는 걸까? DNS도 결국 서버라고, DNS에 요청을 보내서 응답 메시지를 받는 것이다. 이때 DNS에 요청을 보내는 것을 `DNS 리졸버`라고 한다. 줄여서 리졸버라고 부르며, 리졸버는 DNS의 핵심 개념인 IP 주소를 조사하는 `네임 리졸루션`을 실행한다. 

# 리졸버를 이용하여 DNS 서버를 조회한다

![image](https://github.com/user-attachments/assets/0d1ab638-e2e1-4b2c-84ce-64be7d167fb4)


결국 도메인명으로 IP를 조사할 때 

1. 브라우저가 Socket 라이브러리의 리졸버를 활용하여 리졸버를 호출하고 
2. 리졸버가 OS에게 DNS 서버에 조회 요청 의뢰를 보내고
3. DNS 서버가 응답 메시지를 보낸다. 
4. 그리고 리졸버는 이 응답 메시지, 즉 IP 주소를 추출하여 브라우저에서 지정한 메모리 영역에 넣는다. 

# 리졸버 내부의 작동

```bash
<메모리 영역> = gethostbyname(”www.cobinding.tistory.com”)
```

다음과 같은 명령어를 통해 리졸버를 호출하고 DNS 서버에서 IP 주소를 조회할 수 있다. 

리졸버의 동작 방식을 좀 더 자세히 살펴보자. 요청과 응답을 구별해 흐름을 따라가면 된다. 

![image](https://github.com/user-attachments/assets/b9d5b886-7499-47e3-a77d-77c75cadd699)


1. 애플리케이션(브라우저)가 `리졸버`(Socket 라이브러리)를 호출한다. 
2. 리졸버가 OS에게 DNS 서버 조회 의뢰 메시지를 생성해서 `프로토콜 스택`을 호출한다. 
    1. 프로토콜 스택 : OS 내부에 내장된 네트워크 제어용 SW
3. OS에서 받은 메시지를 LAN 어댑터를 통해 DNS 서버로 송신 
4. DNS 서버 응답 메시지가 요청 흐름의 반대 방향을 거쳐서 반송 
5. 리졸버는 최종적으로 OS에게 받은 응답 메시지에서 IP 주소를 추출해서 <메모리 영역>에 저장하고 애플리케이션에 건네줌.


> 💡 <br>
> **DNS는 IP 주소가 필요 없나요?** <br>
> 이는 컴퓨터에 미리 DNS에 대한 TCP/IP 설정이 되어있기 때문에 리졸버를 이를 통해 DNS 서버를 찾는다. 
