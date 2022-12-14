# [Network] SECTION 2.3 네트워크 기기


### 2.3.1 네트워크 기기의 처리 범위

네트워크 기기는 계층에 따라 처리 범위를 나눌 수 있다.

상위 계층을  처리하는 기기는 하위 계층을 처리할 수 있다. (역은 불가하다)

![Untitled 1](https://user-images.githubusercontent.com/40704078/209027266-b337b8db-f4a0-46ed-8800-6d6cf56abc2f.png)

| 계층 | 기기 |
| --- | --- |
| 애플리케이션 | L7 스위치 |
| 인터넷 | 라우터, L3 스위치 |
| 데이터 링크 | L2 스위치, 브리지 |
| 물리 | NIC, 리피터, AP |


### 2.3.2 애플리케이션 계층을 처리하는 기기

L7 스위치(=로드밸런서)

- 서버의 부하를 분산하는 기기이다. (트래픽 분산)
- 클라이언트로부터 오는 요청들을 뒤쪽의 여러 서버로 나누는 역할을 한다.
- 시스템이 처리할 수 있는 트래픽 증가를 목표로 한다.
- 불필요한 외부 데이터 등을 걸러내는 필터링 기능도 할 수 있다. (모니터링)
- 정기적으로 헬스 체크를 이용하여 장애가 발생한 서버가 있는지 감시한다.

```bash
L4 스위치와 L7 스위치 차이

L4 스위치 : IP와 포트를 기반으로 트래픽 분산 (전송 계층 처리 기기)
L7 스위치 : IP, 포트, URL, HTTP 헤더, 쿠키 등을 기반으로 트래픽 분산

클라우드 서비스(ex: AWS)에서 L7 스위치를 이용한 로드밸런싱은 ALB 컴포넌트
L4 스위치를 이용한 로드밸런싱은 NLB 컴포넌트로 한다.
```

```bash
ALB
- HTTP, HTTPS의 특성을 주로 다루는 로드밸런서
- HTTP의 헤더 정보를 이용해 부하 분산을 실시한다.
- SSL 인증서를 탑재할 수 있어 대상 그룹의 EC2를 대신하여 SSL 암/복호화를 대신 진행할 수 있다.
- 외부 방화벽에서 특정 IP만 허용이 필요할 때 ALB 앞단에 NLB를 두거나 AWS Global Accelerator를 설정한다. (NLB만 IP를 고정할 수 있다.)
- ALB 세팅 시 보안그룹을 지정할 수 있다.

NLB
- TCP와 UDP를 사용하는 요청을 받아들여 트래픽 분산을 실시한다.
- HTTP가 아닌 하위 Layer인 TCP Layer에서 처리하므로 HTTP 헤더를 해석하지 못한다.
- 지연시간이 짧다 → L4라서 빠르다.
- 대규모 트래픽 처리에 적합하다.
- IP가 변하지 않는다.
- 보안 그룹 설정을 안한다.
```

헬스체크

- 전송 주기와 재전송 횟수 등을 설정한 후 반복적으로 서버에 요청을 보내는 것
- TCP, HTTP 등 다양한 방법으로 요청 전송
- 요청이 정상적으로 이루어졌다면 정상적인 서버로 판별

서버 이중화

- 로드밸런서의 대표적인 기능
- 로드밸런서가 제공하는 가상 IP 뒷단에 사용 가능한 서버를 여러 대 배치
- 한 서버에 장애가 발생하더라도 다른 서버를 기반으로 안정적인 서비스 운용 가능

![Untitled 2](https://user-images.githubusercontent.com/40704078/209027325-195aad02-f773-45a0-be51-75f9d2d90ca4.png)


### 2.3.3 인터넷 계층을 처리하는 기기

라우터

- 여러 개의 네트워크를 연결, 분할 ,구분 시켜주는 역할
- “다른 네트워크에 존재하는 장치끼리 서로 데이터를 주고받을 때 패킷 소모를 최소화하고 경로를 최적화하여 최소 경로로 패킷을 포워딩” 하는 라우팅을 하는 장비

L3 스위치

- L2 스위치 기능 + 라우팅 기능을 갖춘 장비

| 구분 | L2 | L3 |
| --- | --- | --- |
| 참조 테이블 | MAC 주소 테이블 | 라우팅 테이블 |
| 참조 PDU | 이더넷 프레임 | IP 패킷 |
| 참조 주소 | MAC 주소 | IP 주소 |


### 2.3.4 데이터 링크 계층을 처리하는 기기

L2 스위치

- 장치들의 MAC 주소를 MAC 주소 테이블을 통해 관리
- 연결된 장치로부터 패킷이 왔을 때 패킷 전송 담당
- 목적지가 MAC 주소 테이블에 없다면 전체 포트에 전달
- 하드웨어 기반

```bash
L2와 L3의 차이는?

L2는 하드웨어적인 측면이 크다.
단순히 어떤 포트에 어떤 MAC주소를 가지는 디바이스가 연결되어있는지를 기억하고 그대로 전달
L3는 라우팅이란 개념이 등장, 가상의 주소인 IP주소를 가지고 패킷을 전달하는데
몇번대 IP대역으로 가려면 어떤 라우터(Next Hop)로 가야하는지를 설정해 놓고 
매칭되는 대역으로 가는 패킷은 해당 라우터(Next Hop)로 전달합니다.
```

브리지

- 두 개의 근거리 통신망(LAN)을 상호 접속할 수 있도록 하는 통신망 연결 장치
- 장치에서 받아온 MAC 주소를 MAC 주소 테이블로 관리한다.
- 브리지는 하나의 통신망을 구축할 때 쓰인다.


### 2.3.5 물리 계층을 처리하는 기기

NIC (네트워크 인터페이스 카드)

- 2대 이상의 컴퓨터 네트워크를 구성하는 데 사용한다.
- 네트워크와 빠른 속도로 데이터를 송수신할 수 있도록 컴퓨터 내에 설치하는 확장 카드이다.
- 고유의 식별번호인 MAC주소가 있다.

리피터

- 들어오는 약해진 신호 정도를 증폭하여 다른 쪽으로 전달하는 장치
- 리피터를 통해 패킷이 더 멀리 갈 수 있다. (광케이블 등장 후 현재는 잘 쓰이지 않음)

```bash
통신 signal도 결국 전기신호이므로 옴의 법칙에 따라 저항이 있으면 신호가 감쇄가 된다.
(그 외 외부 노이즈 등)
결국 이로 인해 전송거리 제한이 생기는 데 말 그대로 repeater는 감쇄된 신호를 받아서 
이를 다시 100%세기의 신호로 다시 출력해주는 역할이다.
유선으로 전원을 공급받는 일반 허브 등도 이런 리피터 역할을 수행한다.
```


AP(Access Point)

- 패킷을 복사하는 기기이다.
- 유선 LAN을 연결한 후 다른 장치에서 무선 LAN 기술(와이파이)을 사용하여 무선 네트워크 연결을 할 수 있다.

```bash
와이파이는 무선신호이고 실제 인터넷으로 연결되는 신호는 유선신호이다.
무선신호를 받아서 이를 유선 신호체계로 전달해주는 매개체가 AP이다.
시중에 판매되는 유무선 공유기는 한마디로 라우터에 AP기능이 추가된 것이다.
```
