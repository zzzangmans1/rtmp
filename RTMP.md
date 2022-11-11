# RTMP(Real Time Message Protocol)

## RTMP 란?

어도비 시스템즈 사의 독점 컴퓨터 통신 규약이다. RTMP는 오디오, 비디오, 및 기ㅏ 데이터를 인터넷을 통해 스트리밍할 때 쓰인다. RTMP는 어도비 플레이어와 서버 사이의 통신에 이용된다.

## RTMP 종류

* RTMP(기본) : 1935번 포트 사용, 암호화되지 않은 RTMP, 혹시나 1935번 포트로 시도해서 실패하면 443 포트(RTMPS)나 80 포트(RTMPT)로 재시도함.
* RTMPT(RTMP Tunneled) : RTMP 데이터를 HTTP로 감싼 것. 기본 포트는 80번. HTTP 헤더 때문에 RTMP보다는 크기가 큼.
* RTMPE(Encrypted RTMP) : 128비트로 암호화된 RTM. SSL보다는 가볍지만 SSL 인증같은게 없음. 암호화 채널을 사용하기 때문에 기본 RTMP보다 약간 성능에 영향을 줄 수 있음.
* RTMPTE(Encrypted RTMP Tunneled) : 80번 포트 사용. RTMPT, RTMPE 섞어 놓은 형태. 플래시 플레이어 9,0,115,0 필요. 서버 성능에 영향을 줌.
* RTMFP(Real Time Media Flow Protocol) : UDP에서 동작. 기본 RTMP는 TCP에서 동작. 항상 암호화 된 상태로 데이터를 전송

처음 목표는 오직 플래시에만 쓰이는 것이었다. 그러나 현재는 플래시뿐 아니라 어도비 라이브사이클 데이터 서비시즈 ES와 같은 다른 응용 프로그램에서 RTMP이 쓰이고 있다. 그리고 RTMP 규격은 2009년 1월 20일에 어도비에서 발표했다.

## RTMP Pakcet Header

![image](https://user-images.githubusercontent.com/52357235/201344196-304b70a3-3ede-4c2f-bf3d-cdae49e4922f.png)

## RTMP String 동작 방식

TCP 기반 데이터 전송 프로토콜로 3-way handshake를 사용한다. client가 server로 connection을 요청하고 이를 수락하여 end-to-end 간의 session을 유지한다. 이로인해 RTMP는 매우 안정적으로 오디오 및 비디오 데이터의 고성능 전송이 가능하다.

## RTMP 클라이언트 소프트웨어
크로스-플랫폼 미디어 플레이어 VLC
XBMC라고 붙은 오픈소스 미디어 플레이어
롤도 RTMP로 데이터를 주고 받는다.

## RTMP 서버 소프트웨어

[NGINX기반의 RTMP 미디어 스트리밍 서버](https://github.com/arut/nginx-rtmp-module)

## 참고

https://growthvalue.tistory.com/178

https://ko.wikipedia.org/wiki/%EB%A6%AC%EC%96%BC_%ED%83%80%EC%9E%84_%EB%A9%94%EC%8B%9C%EC%A7%95_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C
