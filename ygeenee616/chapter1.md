도커가 무엇인가요?

도커를 왜 쓰나요?

 

모든 시작에 앞서 우리는 Virtual Machine이나 도커 컨테이너가 없는 1개의 Host 컴퓨터 환경을 생각해봅니다.

컴퓨터 하드웨어 중에는 인터넷과 연결되는 LAN카드와 같은 Network Interface Card, NIC이 있을 것이고 

NIC과 컴퓨터 소프트웨어와의 연결다리 격인 Driver가 있을 것입니다.

소프트웨어는 크게 kernel mode(OS에 해당하는)와 user mode로 나뉘는데,

kernel에는 TCP/IP 프로토콜이 있고, Socket(프로그램이 네트워크에 연결되기 위한 파일)을 활용해서 Process(작동중인 프로그램)이 네트워크를 사용할 수 있게 됩니다.




이 상황에서 우리가 웹서비스를 운영한다 하면, 기본적으로 WEB(Apache), WAS, DB 서버를 띄울 것입니다.

그러나 개발자들 사이 또는 개발과 배포 버전 사이 서로 다른 서버 버전과 환경은 정상적인 운영이 어렵게 할 것이고, 

라이브러리와 바이너리 파일 환경을 맞춰줄 필요가 있습니다.

 

VMware, VirtualBox와 같은 Virtual Machine을 설치하게 되면

기존에는 NIC과 연결된 한 개의 Driver밖에 없었지만, 여러 개의 Driver, Virtual NIC Driver가 생성됩니다.

각 가상 머신은 NIC, 운영체제 라이브러리, Kernel을 포함해 Host의 시스템 자원을 가상화하고 독립화한 공간을 생성하게 되고,

Virtual NIC(가상화된 Guest의 NIC)은 Host의 Virtual NIC Driver를 마치 L2 스위치처럼 사용해 네트워크와 연결되는 것이죠.

따라서 VM 위에 어플리케이션을 구동시키고, 이 VM의 이미지를 그대로 다른 컴퓨터에 옮겨 사용하게 되면

우리는 동일한 운영 환경을 조성할 수 있게 됩니다!

 

그런데 VM의 Kernel과 HW자원 또한 옮겨주어야 하는 걸까요?

🐳 그래서 도커가 탄생합니다.

어플리케이션과 어플리케이션 실행을 위한 binary, Library들을 컨테이너에 담고,

Docker 엔진은 Host의 OS를 포함한 자원을 활용해 각 컨테이너가 활용할 수 있도록 해줍니다.

Docker 컨테이너의 앱이 필요로 하는 연산을 Docker 엔진이 Host의 CPU, RAM 자원들을 활용해 수행한 후 컨테이너에게 전달해주게 됩니다.

VM의 iso 이미지와 같이 도커 이미지를 통해 도커 컨테이너를 생성할 수 있습니다.

 

이러한 원리로 도커는 용량과 효율면에서 VM에 비해서 굉장히 좋아지게 되고, 배포 또한 매우 편리해지게 됩니다.

컨테이너는 WEB, DB와 같이 용도별로 나뉘어 복제 또는 로드밸런싱 부하 관리에도 용이하고

언어에 종속되지 않고 변화 대응에도 빨라 애플리케이션의 독립성과 확장성이 높아지며 개발과 배포가 편리해집니다.

'마이크로서비스'라는 컨테이너로 모듈을 관리하는 가상화 기술은 이러한 컨테이너를 통해 가능하게 됩니다.

이러한 컨테이너들을 관리하기 위해 도커 스웜, 쿠버네티스와 같은 컨테이너 오케스트레이션 플랫폼이 대두됩니다.


 

 

직접 도커를 설치하고, 도커 컨테이너와 이미지를 생성을 실습해보겠습니다.

그 전에, 우리는 도커 컨테이너가 "프로세스"로 

프로세스란 실행중인 프로그램을 말합니다. 구체적으로는, 운영체제로부터 자원을 할당받아서 독립적으로 실행되는 작업 단위입니다.

 

 

 

첫번째, 도커 설치하기
도커는 기본적으로 리눅스 OS에서 구동되는 리눅스 컨테이너입니다.

Windows나 Mac과 같은 다양한 OS에서도 사용가능하지만,

이 경우 Linux Kernel을 탑재하여 사용하며, 일부 기능이 제약될 수 있습니다.

저는 AWS의 우분투 EC2를 활용해서 도커를 사용하였습니다.

 

설치할 때 확인할 사항

- 최신 버전 커널 사용하고 있는지 (3.10 버전 이상 사용해야) 

# 커널 버전 확인 명령어
uname -r
- 지원 기간 내에 있는 배포판인지 (우분투 14.04, CentOS 7 이상)

- 64비트 리눅스인지 (도커는 64비트에 최적화돼 있음)

- sudo 명령어를 통해 설치하거나 root 권한 계정에서 진행해야 함.

 

# 0. 기존 도커 삭제
sudo apt-get remove docker docker-engine docker.io containerd runc

# 1. 패키지 업데이트
sudo apt-get update

# 2. 필수 패키지 설치
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

# 3. Docker 공식 GPG 키 추가
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 4. Docker 공식 apt 저장소를 추가
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
# 5. 패키지 업데이트
sudo apt-get update

# 6. 도커 설치
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 7. 도커 상태 확인
docker info
 

 

두번째, 컨테이너 생성하기
도커 컨테이너는 하나의 프로세스로 처리가 됩니다. 


# 도커 컨테이너 생성 및 실행
# -i 옵션: 상호 입출력 허용 
# -t 옵션: tty 활성화해서 bash 셸 사용하도록 설정 
docker run -i -t ubuntu:14.04

# 도커 이미지 생성
# --name 옵션: 컨테이너 이름 설정 (이름 재설정은 $docker rename [원래  이름] [새이름])
docker create -i -t --name mycentos centos:7

# 도커 컨테이너 시작
docker start mycentos

# 도커 컨테이너 내부로 들어가기
docker attach mycentos

# 컨테이너 정지
docker stop mycentos

# 컨테이너 빠져나가고 정지
# 정지없이 컨테이너 셸에서만 빠져나오려면 Ctrl+P,Q
exit

# 도커 공식 이미지 저장소에서 이미지 내려받기
docker pull [이미지 이름]:[버전]

# 도커 엔진에 존재하는 이미지 목록 출력
docker images

# 도커 컨테이너 목록 확인
# -a 옵션: 정지된 컨테이너를 포함한 모든 컨테이너 출력
# 출력되는 값 [ID] 컨테이너 ID 앞 일부분 [COMMAND] /bin/bash와 같이 컨테이너가 시작할때 실행될 명령어
docker ps -a

# 컨테이너 삭제
# 한 번 삭제한 컨테이너는 복구할 수 없음 주의
# 정지된 컨테이너만 삭제할 수 있음
docker rm [컨테이너 이름]
 

이제 도커 컨테이너를 외부에 노출시켜 접속할 수 있도록 지정해봅시다.

VM 와 마찬가지로 컨테이너는 가상 IP 주소를 할당받습니다.