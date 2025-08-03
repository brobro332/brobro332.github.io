---
title: 🚀 OCI Instance 구축
date: 2024-12-22 23:10:00 +0900
categories:
  - Infra
tags:
  - Infra
  - OCI
---

### 왜 `OCI`를 도입했는가?
- `OCI`에서는 `Always Free Resources`를 제공한다.
- 프로젝트 배포에 있어서 비용 문제는 무엇보다 가장 중요한 문제라고 생각한다.


### `Always Free Resources` 문서
[`Always Free Resources` 문서 링크](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm?Highlight=free)


### `Instance` 구축 과정
#### ✅ `VCN` 설정
![](/assets/image/Pasted%20image%2020250531152906.png)
- `Networking` > `Virtual cloud networks` 메뉴로 들어와서 "`Create VCN`" 버튼을 누르면 위 화면이 나온다. 
- `VCN`(`Virtual Cloud Network`)은 사용자가 정의하는 가상 네트워크다.
- `Instance`, `DB` 등 `OCI`(`Oracle Cloud Interface`) `Resource`가 통신할 수 있는 환경을 제공한다.
- `VCN`을 설정하는 과정에서 다음 목록들도 함께 설정을 해야 한다.
	1. `IP` 주소 범위 설정
		- 네트워크 간 충돌을 막기 위해서 `IP` 주소의 범위를 설정해야 한다.
		- 가령 `192.168.0.0`/`16`으로 지정한다면 `IP`의 주소는  `192.168.0.0` ~ `192.168.255.255`가 된다.
	2. 서브넷 설정
		- 트래픽 분산이나 별도의 보안 정책을 적용하는 등의 이점을 위해 네트워크를 논리적, 물리적으로 분리하여 사용하려면 서브넷을 설정해야 한다.
		- 가령 `192.168.0.0`/`24`로 지정한다면 `IP`의 주소는 `192.0.0.168` ~ `192.0.0.255`가 된다.
		- 서브넷의 범위는 `IP` 주소 범위 안에 있어야 한다.
	3. 인터넷 게이트웨이 설정
		- `VCN` 내 `Resource`가 공용 인터넷과 통신할 수 있도록 인터넷 게이트웨이 설정을 해야 한다.
	4. 라우트 테이블 설정
		- 라우트 테이블은 `VCN` 내 `Resource`가 인터넷, 네트워크, 또는 다른 `VCN`으로 트래픽을 전송할 때 적절한 경로를 결정한다.
	5. 보안규칙 설정
		- 특정 포트에 대해서 허용 `IP`나 그 범위를 설정하는 것이다.

#### ✅ `Instance` 생성
![](/assets/image/Pasted%20image%2020250531153626.png)
![](/assets/image/Pasted%20image%2020250531153640.png)
- `Instance` 이미지와 형태를 선택해야 한다.

![](/assets/image/Pasted%20image%2020250531153749.png)
- 앞서 설정한 `VCN`과 서브넷을 설정해야 한다.

![](/assets/image/Pasted%20image%2020250531153825.png)
- `SSH Key` 설정인데, 공개키 붙여넣기 방식이 보안에 더 좋다고 한다.
- 비공개 키와 공개 키는 따로 저장해두어야 한다.

![](/assets/image/Pasted%20image%2020250531153917.png)
- `Volume`을 설정해야 한다.
- 여기까지 설정을 마쳤다면 우측 하단의 "`Create`" 버튼을 누르면 `Instance`가 생성된다.

#### ✅ `Instance` 설정
![](/assets/image/Pasted%20image%2020250531154101.png)
- `Compute` > `Instances` 메뉴에 들어가 생성한 `Instance`를 클릭하면 정보를 확인할 수 있다.
- `Public IP`는 유동적이어서  `Instance`를 중단하고 시작할 때 계속 값이 변하므로 `Reserved IP`를 등록해야 한다.
-  좌측 하단의 `Attached VNICs` 메뉴로 들어가서 `VNIC`를 클릭하면 다음과 같이 메뉴가 보이는데, `IPv4 Addresses` 메뉴로 들어가야 한다.

![](/assets/image/Pasted%20image%2020250531154323.png)
- "`Edit`" 버튼을 클릭하여 `No public IP`로 설정한 후 저장한다.

![](/assets/image/Pasted%20image%2020250531154357.png)
- `Reserved IP`를 설정하기 위해 다시  "`Edit`"  버튼을 누르자.

![](/assets/image/Pasted%20image%2020250531154448.png)
- 위와 같이 설정하고 저장하면 된다.
- 이제 `Instance` 기본 설정은 마쳤다.

#### ✅ `Instance` 원격 접속
![](/assets/image/Pasted%20image%2020250531154605.png)
- 생성한 `Instance`를 클릭하면 위처럼 정보를 확인할 수 있다. 
- `Public IP` 주소와 사용자명은 원격 접속 시 필요하므로 기억해 두자.

![](/assets/image/Pasted%20image%2020250531154704.png)
- 필자는 `MobaXterm`을 사용할 것이다.

![](/assets/image/Pasted%20image%2020250531154731.png)
- `Remote host`에는 `Public IP` 주소를 입력하면 된다. 
- 사용자명도 입력해야 한다.
- `Instance` 생성할 때 저장한 비밀 키를 등록해야 원격으로 접속할 수 있다.

![](/assets/image/Pasted%20image%2020250531154845.png)
- 접속까지 성공했다.
- 이제 `DB` 등 필요한 `Resource`를 구축하면 된다.