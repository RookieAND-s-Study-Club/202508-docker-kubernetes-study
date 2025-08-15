# 2.1 도커 이미지와 컨테이너

## 2.1.1 도커 이미지

• 도커 이미지는 컨테이너를 생성할 때 필요한 요소이며, 가상 머신 생성 시 사용하는 ISO 파일과 비슷한 개념을 가집니다.
• 이미지는 여러 계층으로 된 바이너리 파일로 존재하며 컨테이너 생성 및 실행 과정에서 Read-Only로 쓰입니다.

### 도커 이미지 이름 구성

**형식**: `[저장소명]/[이미지명]:[태그]`

• **저장소**: 이미지가 저장된 장소를 의미합니다. 저장소 이름이 명시되지 않은 경우에는 Docker Hub에 위치한 공식 이미지임을 의미합니다.
• **이미지명**: 해당 이미지가 어떤 역할을 하는지 나타냅니다. (ubuntu, redis, mysql 등) 이는 생략할 수 없으므로 반드시 기입해야 합니다.
• **태그**: 이미지의 버전 관리 혹은 Revision 관리에 사용됩니다. 태그를 생략할 경우 도커 엔진은 이미지의 태그를 latest로 인식합니다.

## 2.1.2 도커 컨테이너

• 도커 이미지를 기반으로 컨테이너 생성 시 이미지의 목적에 맞는 파일이 들어있는 파일 시스템과 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간을 의미합니다.
• 컨테이너는 이미지를 Read Only로만 사용하며 이미지에서 변경된 사항은 별도의 "컨테이너 계층"에서 사용하므로 컨테이너 내 변경 사항이 원래 이미지에 영향을 주지 않습니다.
• 각각의 컨테이너는 각자 독립된 파일 시스템을 제공받고 호스트와 분리되어 특정 컨테이너의 변경 사항이 다른 컨테이너 및 호스트에 영향을 주지 않습니다.

### VM ISO 파일과 도커 이미지의 유사점

• VM ISO는 OS 설치 파일과 필수 구성 요소를 묶어둔 패키지 파일입니다.
• 도커 이미지 또한 특정 앱을 실행하기 위해 필요한 파일 시스템 스냅샷을 묶어둔 패키지입니다.
• 둘 다 실행에 필요한 데이터를 패키징해서 다른 곳에 활용할 수 있는 형태로 만들어진 바이너리 파일이라는 점에서 같습니다.

# 2.2 도커 컨테이너 다루기

## 2.2.1 컨테이너 생성

### 기본 명령어

**`docker run [option] [image]`** - 이미지를 기반으로 컨테이너를 생성할 수 있습니다.

### 주요 옵션

• **`-i`**: 표준 입력(STDIN)을 열어두는 옵션으로, 컨테이너가 실행 중일 때 터미널 입력을 받을 수 있도록 합니다.
• **`-t`**: 가상 터미널(TTY)을 할당하여 출력이 터미널 형태로 표기되어 색상, 줄바꿈, Prompt 등이 동작합니다.

### 컨테이너 실행 및 진입

• `docker run`으로 컨테이너 생성 및 실행하면 컨테이너 내부로 진입할 수 있습니다.
• 기본 사용자는 `root` 이며 호스트 이름은 무작위 16진수 이름입니다.

### 컨테이너 종료 방법

• **완전 종료**: 컨테이너 내부에서 호스트 OS로 돌아가려면 터미널에서 `exit`를 입력하거나 Ctrl + D를 입력하면 됩니다.
  - 위 방법은 컨테이너를 이탈하는 동시에 컨테이너를 정지시킵니다.
• **일시 이탈**: 컨테이너를 정지시키지 않고 빠져나오려면 Ctrl + P, Q를 입력하면 됩니다. (컨테이너의 Shell에서만 빠져나옴)

### 관련 명령어

• **`docker images`**: 도커 엔진에 존재하는 이미지의 목록을 출력합니다.
• **`docker create`**: 컨테이너를 생성만 할 뿐 내부로 진입하지 않습니다. 명령어 실행 시 무작위의 16진수 해시값을 출력하는데 이는 컨테이너의 고유 ID 값입니다.
• **`docker inspect`**: 컨테이너의 ID를 확인할 수 있습니다.
• **`docker start`**: 컨테이너를 시작할 수 있습니다.
• **`docker attach`**: 컨테이너의 내부로 들어갈 수 있습니다.

### docker run vs docker create

• **`run`**: 이미지가 없다면 pull을 하고, 컨테이너를 생성하고, 이를 시작한 후에 `-it` 옵션이 있다면 내부로 진입합니다.
• **`create`**: 이미지를 pull한 이후 컨테이너를 생성하는 것에서 끝납니다.

### 참고사항

• 컨테이너를 대상으로 하는 명령어는 컨테이너의 이름 대신 ID를 사용할 수 있습니다.
• 16진수 해시 값이 너무 길기에 앞의 2~3글자만 입력해도 됩니다.

## 2.2.2 컨테이너 목록 확인

### 기본 명령어

**`docker ps`** - 현재 도커 엔진 내 컨테이너의 목록을 열람할 수 있습니다.

• 컨테이너 ID, 이미지, 실행 시 명령어, 생성 시간, 상태, 포트, 컨테이너 이름을 알 수 있습니다.
• 기본적으로 정지되지 않은 상태의 컨테이너만 출력하기 때문에 모든 상태를 보고 싶다면 **`-a`** 옵션을 추가합니다.

### 출력 정보 설명

• **CONTAINER ID**: 컨테이너에게 자동으로 할당되는 고유 ID입니다. ID의 일부분만 출력되며 전체를 알기 위해서는 `docker inspect` 명령어를 사용합니다.
• **IMAGE**: 컨테이너 생성 시 쓰인 이미지의 이름입니다.
• **COMMAND**: 컨테이너가 시작될 때 실행될 명령입니다. 기본적으로 이미지에 내장되며 이를 Override하고 싶다면 `docker run`이나 `docker create` 명령어의 맨 끝에 덮어쓸 명령어를 입력합니다.
• **CREATED**: 컨테이너 생성 이후 경과된 시간을 나타냅니다.
• **STATUS**: 컨테이너의 상태를 나타냅니다.
• **PORTS**: 컨테이너가 개방한 포트와 호스트와 연결된 포트를 나열합니다.
• **NAMES**: 컨테이너의 고유 이름입니다. `--name` 옵션으로 지정 가능하며 미지정 시 무작위의 형용사 + 명사 조합으로 임의의 이름을 생성합니다. `docker rename` 명령어로 이름 변경이 가능합니다.

### 출력 형식 커스터마이징

• `--format` 옵션을 통해 Go 템플릿 문법을 활용하여 위 내역 중 원하는 정보만 출력할 수 있습니다.

```shell
docker ps --format "table {{.ID}}\t{{.Status}}\t{{.Image}}"
```

## 2.2.3 컨테이너 삭제

### 기본 명령어

**`docker rm [컨테이너명/ID]`**

• 더 이상 사용하지 않는 컨테이너를 삭제할 수 있습니다.
• 한번 삭제한 컨테이너는 복구가 불가능하므로 신중하게 결정해야 합니다.
• 실행 중인 컨테이너는 삭제할 수 없으므로, `docker stop` 명령어를 사용하여 먼저 컨테이너를 정지하거나 `-f` 옵션으로 강제 종료가 가능합니다.

### 일괄 삭제 방법

• **`docker container prune`**: 정지된 상태의 모든 컨테이너를 삭제할 수 있습니다.
• **Shell 명령어 조합 사용**:

```shell
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```

## 2.2.4 컨테이너를 외부에 노출

### 컨테이너 IP 할당

• 컨테이너는 VM과 마찬가지로 가상 IP 주소를 호스트로부터 할당받습니다.
• 도커는 컨테이너에게 `172.17.0.X` 형식의 IP를 순차적으로 할당합니다.

### 네트워크 인터페이스

• 도커의 NAT IP인 172.17.0.2를 할당받은 eth0 인터페이스와 로컬 호스트인 lo 인터페이스를 볼 수 있습니다.
• 별도의 설정이 없는 경우 해당 컨테이너는 외부에서 접근할 수 없습니다. (NAT 뒤에 있기 때문에 외부에서 직접 접근 불가)
• 컨테이너에서 외부로 요청을 보낼 때는 NAT를 거쳐 Host의 공인 IP로 요청을 보내기에 가능합니다.
• NAT는 "내부 → 외부" 요청은 기억해서 응답을 되돌려줄 수 있지만, "외부 → 내부" 요청은 어느 내부 IP(컨테이너)로 보내야 할지 모릅니다.

### 포트 포워딩

• 외부에 컨테이너를 노출하기 위해서는 컨테이너 `eth0`의 IP와 포트를 호스트의 IP와 포트에 바인딩(포트 포워딩)해야 합니다.
• `docker run`에서 **`-p`** 옵션을 통해 컨테이너의 포트를 호스트의 포트와 바인딩하여 연결할 수 있도록 해줍니다.
• **입력 형식**: `[호스트의 포트]:[컨테이너의 포트]`
• 호스트의 포트를 생략하면 호스트의 포트 중 하나와 컨테이너의 지정된 포트가 포워딩됩니다.
• **`docker port`** 명령어로 바인딩된 포트 확인이 가능합니다.

## 2.2.5 컨테이너 애플리케이션 구축

### 컨테이너 구성 원칙

• 컨테이너 하나에 여러 개의 애플리케이션을 설치할 수 있으나, 각 목적별로 컨테이너를 구분하는 것이 이미지 관리 및 독립성 유지 측면에서 바람직합니다.
• 한 컨테이너에 프로세스 하나만 실행하는 것이 도커의 기본 철학이기도 합니다.

### docker run의 주요 옵션들

1) `-d` (Detached 모드)

• Detached 모드로 컨테이너 실행 (백그라운드에서 동작하는 애플리케이션)
• 해당 모드로 컨테이너를 실행할 경우 입출력이 없는 상태로 실행하므로 내부에서 프로그램이 터미널을 차지하는 포그라운드로 실행되어 사용자의 입력을 받지 않습니다.
• 포그라운드 프로그램이 실행되지 않으면 컨테이너가 바로 종료되므로 유의해야 합니다.

2) `-e` (환경변수 설정)

• 컨테이너 내부의 환경 변수를 설정할 수 있습니다.
• 컨테이너화된 애플리케이션이 환경 변수로부터 값을 가져와 작업할 수 있도록 합니다.
• 컨테이너 내부에서 echo로 환경 변수를 출력하면 (`echo ${ENVIRONMENT_NAME}`) 설정 값을 확인할 수 있습니다.

3) `--link` (컨테이너 연결)

• 각 컨테이너별로 할당된 NAT IP를 알 필요 없이 항상 컨테이너에 Alias(별칭)로 접근 가능하도록 합니다.
• `[컨테이너명]:[별칭]`으로 값을 받으며 추후 해당 컨테이너에서 다른 컨테이너로 요청을 보내고 싶다면 별칭을 사용하면 됩니다.
• 하지만 현재 해당 옵션은 Deprecated된 상태이며 Docker Bridge를 사용하는 것이 더 좋습니다.

```shell
docker exec wordpress curl mysql:3306 --http0.9 --silent
```

### 컨테이너 실행 명령어

• **`docker attach`**는 Detached 모드로 실행된 컨테이너에서 실행 중인 프로그램의 로그만 볼 수 있다는 단점이 있습니다.
• 이를 상호 입출력이 가능한 형태로 **`docker exec`** 명령어를 사용하여 내부에서 실행한 명령어의 결과를 반환하도록 할 수 있습니다.
• exec로 컨테이너 내부에 들어왔어도 exit 입력 시 컨테이너가 종료되지 않는데, 포그라운드 모드로 동작하는 프로세스가 있기 때문입니다.

## 2.2.6 도커 볼륨

### 볼륨의 필요성

• 도커 컨테이너의 변경 사항은 이미지와 별도로 관리됩니다. 컨테이너 레이어는 읽고 쓰기가 가능하지만, 이미지 레이어는 읽기만 가능합니다.
• 하지만 컨테이너를 삭제하면 컨테이너 레이어에 저장된 파일도 모두 삭제되기에 이를 복구할 수 있는 수단이 없습니다.
• 이를 해결하기 위해 도커에서는 볼륨이라는 개념을 도입했습니다.

### 1) 호스트와 볼륨을 공유하는 방법

1. 기본 사용법

• `docker run` 명령어에 **`-v`** 옵션을 통해 볼륨을 지정할 수 있습니다.
• **입력 값**: `[호스트의 공유 디렉토리]:[컨테이너의 공유 디렉토리]`

2. 특징

• 호스트에 미리 해당 디렉토리가 생성되지 않았어도 Docker에서 자동으로 이를 생성하며 컨테이너 삭제 시에도 호스트의 공유 디렉토리에 잔존합니다.
• 리눅스의 마운트 네임스페이스 기능을 활용해서 호스트의 디렉토리를 컨테이너 파일 시스템 트리의 특정 경로에 직접 연결합니다. (Kernel 레벨에서 Mount)

3. 주의사항

• 호스트 내 공유 디렉토리에 특정 파일이 이미 존재하는 상황에서 볼륨을 공유하는 컨테이너가 켜질 경우 컨테이너의 디렉토리가 호스트의 디렉토리가 되어 버립니다. (이미지에 존재하는 기존 파일 덮어씌워짐)
• 여러 개의 `-v` 옵션을 사용해서 각기 다른 디렉토리를 동기화할 수 있습니다.

### 2) 볼륨 컨테이너를 활용하는 방법

1. 기본 사용법

• 컨테이너 생성 시 **`--volumes-from`** 옵션을 활용하여 컨테이너의 볼륨 디렉토리를 공유할 수 있습니다.
• 직접 볼륨을 생성하는 것이 아니라 `-v` 옵션을 적용한 컨테이너를 통해 공유하는 것임을 유의해야 합니다.

2. 활용 방법

• 여러 개의 컨테이너가 동일한 컨테이너에 볼륨을 공유함으로써 별도의 볼륨 컨테이너를 지정하고 데이터를 간접적으로 공유받는 방식도 가능합니다.

3. 사용 사례

• 여러 애플리케이션이 같은 데이터 디렉토리를 써야 할 때
• 권한 및 설정 관리를 단순화하고 싶을 때 등 특정 상황에서 쓰입니다.

## 3) 도커가 관리하는 볼륨을 생성하는 방법

1. 볼륨 관리 명령어

• **`docker volume create`**: 볼륨을 생성합니다.
• **`docker volume ls`**: 생성된 볼륨 목록을 열람합니다. 플러그인 드라이버를 활용하여 여러 종류의 스토리지 백엔드를 쓸 수 있습니다. (기본은 `local`)
• 이 볼륨은 로컬 호스트에 저장되어 도커 엔진에 의해 생성 및 삭제가 관리됩니다.

2. 볼륨 사용법

• `-v` 옵션으로 볼륨을 마운트하고 싶은 경우 `[볼륨명]:[컨테이너 공유 디렉토리]`로 입력 값을 지정합니다.
• 볼륨은 디렉터리 하나에 상응하는 관계이며 도커 엔진에서 관리합니다.
• 도커 볼륨 또한 호스트에 내용을 저장하지만 파일이 실제로 어디에 저장되었는지를 사용자가 알 필요 없이 볼륨명으로 쉽게 접근이 가능하도록 해준다는 점에서 용이합니다.

3. 자동 볼륨 생성

• `docker volume create` 없이도 컨테이너 생성 및 실행 단계에서 `-v` 옵션 입력 시 이를 시행하도록 할 수 있습니다.
• 공유할 디렉토리의 위치를 `-v` 옵션에 입력하면 도커에서 해당 디렉토리에 대한 볼륨을 자동 생성하며 16진수 형태의 이름을 가집니다.

### 볼륨 관리 명령어

1. 볼륨 정보 확인

• **`docker inspect`** 명령어를 사용해서 해당 볼륨이 호스트 내 실제 어느 디렉토리에 저장되는지 알 수 있습니다.
• **`--type`** 인자에 `image`, `volume`을 입력하여 MountPoint, Driver, Name 등 볼륨 정보를 열람할 수 있습니다.
• 도커 명령어는 docker 뒤에 container, image, volume 등을 명시하여 구성 단위를 제어합니다. (같은 inspect여도 컨테이너냐, 볼륨이냐의 차이)

2. 볼륨 저장 위치

• Docker에서 직접 관리하는 Volume의 경우 **`/var/lib/docker/volumes`**에 저장됩니다. (볼륨명을 기준으로 디렉토리가 나뉘어 있음)

3. 볼륨 정리

• **`docker volume prune`** 명령어를 사용하여 현재 컨테이너에서 직접적으로 사용하지 않은 볼륨을 제거할 수 있습니다.
• 컨테이너가 종료되어도 볼륨은 자동으로 삭제되지 않기 때문에 직접 제거하는 과정이 필요합니다. (아니면 이름을 명시하여 관리하거나)

### --mount 옵션

1. --mount 옵션 사용법

• **`-v`** 옵션 대신 **`--mount`** 옵션을 사용할 수 있습니다.

2. --mount 옵션 설정

• **`--type`**: volume 혹은 bind를 넣으며 bind의 경우 호스트 내 디렉토리와 컨테이너 내 경로를 직접 연결하고, volume은 Docker Volume을 사용할 때 씁니다.
• **`--source`**: 마운트할 도커 볼륨 혹은 호스트 내 디렉토리 경로를 받습니다.

## 2.2.7 도커 네트워크

### 네트워크 기본 개념

• 도커 컨테이너는 내부 IP를 생성 시 순차적으로 할당받으며 컨테이너 재시작 시에도 변경할 수 있습니다.
• 내부 IP의 경우 도커가 설치된 호스트(내부)에서만 사용 가능하기에 외부와 연결이 필요합니다.

### 네트워크 인터페이스 생성

• 이 과정은 컨테이너를 시작할 때마다 호스트에 `veth`라는 네트워크 인터페이스를 생성하여 이루어집니다.
• 각 컨테이너에 외부와의 네트워크를 제공하기 위해서 도커 엔진은 컨테이너 생성 시 가상 네트워크 인터페이스를 호스트에 생성하며 이름은 `veth`(virtual eth)로 시작합니다.
• 실행 중인 컨테이너 수만큼 `veth` 네트워크가 있음을 호스트에서 확인할 수 있습니다.
• `docker0` 브리지는 각 `veth` 인터페이스와 바인딩되어 호스트의 `eth0` 인터페이스와 이를 이어주는 역할을 합니다.

### 네트워크 드라이버

1. 기본 설정

• 컨테이너 생성 시 기본적으로는 `docker0` 브릿지를 통해 외부와 통신 가능한 환경을 사용할 수 있습니다.
• 하지만 사용자의 선택에 따라 여러 네트워크 드라이버를 사용할 수도 있습니다.

2. 지원되는 드라이버

• **도커가 자체적으로 제공하는 네트워크 드라이버**: bridge, host, none, container, overlay
• **서드 파티 플러그인 솔루션**: weave, flannel, openvswitch 등
• **`docker network ls`**로 네트워크 목록을 확인할 수 있습니다. (이미 bridge, host, none은 존재함)

### 1) 브릿지 네트워크

1. 사용자 정의 브릿지 생성

• `docker0`를 사용하지 않고 사용자 정의 브릿지 네트워크를 생성할 수 있습니다.
• **`docker network create --driver bridge [브릿지명]`**으로 생성 가능합니다.
• `docker run/create` 명령어에 **`--net`** 옵션의 값을 지정하면 컨테이너가 이 네트워크를 사용하도록 설정합니다.
• 컨테이너 내부에서 ifconfig 입력 시 새로운 IP 대역이 할당됨을 알 수 있습니다. 브리지 타입의 네트워크 생성 시 도커는 IP 대역을 순차적으로 할당합니다.

2. 네트워크 관리

• 생성된 사용자 정의 네트워크는 **`docker network [disconnect/connect] [네트워크명] [컨테이너]`**를 통해 컨테이너에 부착 및 해제 가능합니다.
• 단, none 및 host 네트워크 등 특별한 모드에서는 사용이 불가능합니다. (특정 IP 대역을 갖는 모드에서만 사용이 가능함)

3. 네트워크 세부 설정

• **`--subnet`**으로 네트워크의 서브넷을, **`--ip-range`**로 IP 대역폭을, **`--gateway`**로 게이트웨이를 설정할 수 있습니다.
• 단, `--subnet`과 `--ip-range`는 같은 대역폭이어야 합니다.

4. 네트워크 별칭

• `docker run`의 **`--net-alias`** 명령어를 함께 사용할 경우 특정 호스트명으로 컨테이너를 여러 개 접근할 수 있습니다.
• 동일한 브릿지 네트워크 내 다른 컨테이너에서 특정 호스트명으로 ping 요청을 보낼 경우 각 컨테이너의 IP로 ping이 전송됩니다.
• 요청 전송 시 IP가 매번 달라지는 것은 라운드 로빈 방식이며, 도커 엔진 내 DNS가 호스트명을 `--net-alias`로 해당 이름을 지정한 컨테이너로 변환하기 때문입니다.
• 호스트 이름 변환을 도커 내장 DNS로 요청하고 IP 목록을 라운드 로빈 방식으로 반환합니다. DNS의 IP는 127.0.0.11입니다.

### 2) 호스트 네트워크

1. 기본 개념

• 네트워크를 호스트로 설정하면 호스트의 네트워크 환경을 그대로 사용할 수 있습니다.
• 호스트 드라이버의 네트워크는 별도 생성을 거치지 않고 기존 `host`라는 이름을 사용하면 됩니다.

2. 사용 방법

• **`--net`** 옵션에 `host`를 기술하면 적용됩니다.
• 호스트 머신에서 설정한 이름도 컨테이너가 물려받기에 컨테이너의 호스트명도 엔진이 설치된 호스트의 이름으로 설정됩니다.
• 컨테이너 내부의 앱을 별도 포트 포워딩하지 않고 바로 연결할 수 있습니다.

### 3) 논 네트워크

• 아무런 네트워크를 사용하지 않는다는 의미입니다.
• **`--net`** 옵션에 `none`을 넣으면 적용됩니다.

### 4) 컨테이너 네트워크

1. 기본 개념

• **`--net`** 옵션에 container를 입력하면 다른 컨테이너의 네트워크 네임스페이스 환경을 공유할 수 있습니다.
• 공유되는 속성은 내부 IP, 네트워크 인터페이스 내 MAC 주소 등입니다.

2. 사용 방법

• **`--net`**의 값으로 `container:[컨테이너ID]`를 입력합니다.
• 다른 컨테이너의 네트워크 환경을 공유하므로 컨테이너 생성 시 새로운 `veth`도 생성되지 않고 NAT IP도 할당되지 않습니다.
• 두 컨테이너의 `eth0` 속성 또한 당연히 동일합니다.

## 2.2.8 컨테이너 로깅

### 1) json-file 로그 사용

1. 기본 로그 확인

• 도커는 컨테이너의 STDIN, STDERR 로그를 별도의 메타데이터 파일로 저장하여 이를 확인하는 명령어를 제공합니다.
• **`docker logs`** 명령을 사용하여 컨테이너 내부에서 출력을 열람할 수 있습니다.

2. docker logs 옵션

• **`--tail`**: 마지막 로그 줄부터 N개의 로그만 볼 수 있습니다.
• **`--since`**: 유닉스 시간을 입력하여 특정 시간 이후의 로그를 볼 수 있습니다.
• **`-t`**: 타임스탬프를 표기할 수 있습니다.
• **`-f`**: 컨테이너에서 실시간으로 출력되는 내용을 확인하기 위해 로그를 스트림으로 확인할 수 있습니다.

3. 로그 저장 위치

• 기본적으로 컨테이너 로그는 JSON 형태로 도커 내부에 저장됩니다.
• **`/var/lib/docker/containers/${CONTAINER_ID}`** 디렉토리 내 **`${CONTAINER_ID}-json.log`** 파일로 저장됩니다.

4. 로그 크기 제한

• 컨테이너 내부의 출력이 너무 많으면 JSON 파일 사이즈가 커질 수 있기 때문에 **`--log-opt`** 옵션으로 JSON 파일의 최대 크기도 지정할 수 있습니다.
• **`max-size`**: 최대 사이즈를 의미합니다.
• **`max-file`**: 로그 파일의 최대 개수를 의미합니다.

5. 기본 로깅 드라이버 변경

• 로깅 드라이버는 기본적으로 json-file로 세팅하나, Docker Daemon 시작 옵션에서 **`--log-driver`** 옵션으로 Default Logging Driver 변경이 가능합니다.

### 2) syslog 로그 사용

1. syslog 개념

• Syslog는 Unix/Linux 계열 운영체제에서 로그 메시지를 전송·저장·분석하는 표준 프로토콜 및 프레임워크입니다.
• 커널, 시스템 서비스, 애플리케이션 등에서 생성된 로그를 중앙에서 관리할 수 있도록 설계되었습니다.
• 생성된 로그는 로컬 파일(/var/log/)에 기록할 수도 있고, 네트워크를 통해 원격 Syslog 서버로 보낼 수도 있습니다.

### rsyslog 설치 및 설정

1. rsyslog 설치

```shell
sudo apt-get update
sudo apt-get install rsyslog
```

2. 서버 역할 구분

• **수신 서버**: rsyslog가 TCP/UDP 포트(기본 UDP 514)를 열어 다른 서버에서 오는 로그를 받습니다.
• **송신 서버**: rsyslog를 통해 생성된 로그를 특정 서버로 전송합니다.

### Docker에서 syslog로 전송하기

1. 기본 사용법

```shell
docker run -d \
  --name my_app \
  --log-driver=syslog \
  --log-opt syslog-address=tcp://192.168.0.100:514 \
  --log-opt tag="myapp_container" \
  --log-opt syslog-facility=daemon \
  nginx
```

2. syslog 옵션 설명

• **`syslog-address`**: 로그를 보낼 syslog 서버 주소 (`udp://` 또는 `tcp://`)
• **`tag`**: syslog 메시지에 붙는 태그. 검색·분류 시 유용
• **`syslog-facility`**: 로그 저장 범주 지정 (`daemon`, `mail`, `user`, `kern` 등)

3. 프로토콜 선택

• **UDP**: 속도가 빠르지만 신뢰성이 낮음
• **TCP**: 전송 신뢰성이 높음

## 3) fluentd 로깅

### fluentd 개념

1. 데이터 수집기의 역할

• Fluentd는 로그 수집·필터링·변환·전송을 담당하는 오픈소스 데이터 수집기입니다.
• 데이터 포맷으로 JSON을 사용하면 로그 파싱과 검색이 편리하며 다양한 출력 플러그인(MongoDB, Elasticsearch, S3 등)을 지원합니다.

### fluent.conf 파일 설정

1. 설정 파일 예시

```conf
<source>
  @type forward
  port 24224
</source>

<match docker.**>
  @type mongo
  host mongo
  port 27017
  database logs
  collection app_logs
  include_tag_key true
  <buffer>
    flush_interval 5s
  </buffer>
</match>
```

2. 설정 설명

• 로그의 태그가 `docker`로 시작하면 이를 MongoDB에 전달하도록 세팅 (<match docker.**>)

### Docker에서 fluentd 실행

1. 컨테이너 실행

```shell
docker run -d \
  --name fluentd \
  -v $(pwd)/fluent.conf:/fluentd/etc/fluent.conf \
  -p 24224:24224 \
  fluent/fluentd
```

2. 설정 옵션 설명

• **Volume mount**: -v로 호스트의 fluent.conf를 컨테이너 설정 파일로 사용합니다.
• **log-driver**: 애플리케이션 컨테이너에서 --log-driver=fluentd로 지정 가능합니다.
• **address**: Fluentd 서버 주소(--log-opt fluentd-address=...)로 설정합니다.

## 4) AWS CloudWatch Logs

### 설정 단계

• IAM 권한 생성
• 로그 그룹 생성
• 로그 그룹에 로그 스트림 생성
• EC2 인스턴스 생성 및 로그 전송

### 사용 시나리오

• AWS 클라우드 환경에서 중앙 집중식 로그 관리가 필요한 경우
• CloudWatch의 모니터링 및 알람 기능과 연계하여 로그 기반 알림을 설정하고자 할 때
• 다른 AWS 서비스(Lambda, S3 등)와 로그 데이터를 연동해야 하는 경우

---

# 2.2.9 컨테이너 자원 할당 제한

## 자원 제한의 필요성

• 컨테이너가 무제한으로 시스템 자원을 사용하면 호스트 시스템이나 다른 컨테이너에 영향을 줄 수 있습니다.
• 멀티테넌트 환경에서 공정한 자원 분배와 시스템 안정성을 보장하기 위해 자원 제한이 필수적입니다.
• 성능 예측 가능성과 비용 관리 측면에서도 중요한 역할을 합니다.

## 자원 제한 확인 및 수정

1. 현재 설정 확인

• **`docker inspect`** 명령어 출력 결과 내 "HostConfig" 속성 중 현재 컨테이너에 설정된 자원 제한을 확인할 수 있습니다.

2. 설정 변경

• **`docker update [변경할 자원 제한] [컨테이너명]`**으로 변경 사항을 수정할 수 있습니다.

## 1) 컨테이너 메모리 제한

### 메모리 제한 기본 사용법

• `docker run` 명령어에 **`--memory`**를 지정하여 컨테이너의 메모리를 제한할 수 있습니다.
• 단위는 `m`(MB) `g`(GB)를 사용하며 최소 제한 가능 메모리는 6MB입니다.

### SWAP 메모리 설정

• **`--memory-swap`**으로 SWAP 메모리 설정도 가능하며 기본적으로 메모리 제한의 2배가 걸립니다.

### 메모리 제한 예시

```shell
# 512MB 메모리 제한
docker run --memory 512m nginx

# 1GB 메모리와 2GB 스왑 설정
docker run --memory 1g --memory-swap 2g nginx

# 스왑 비활성화 (메모리와 스왑을 같은 값으로 설정)
docker run --memory 1g --memory-swap 1g nginx
```

### 메모리 사용량 모니터링

• **`docker stats`** 명령어로 실시간 메모리 사용량을 확인할 수 있습니다.
• 메모리 한계에 도달하면 컨테이너 내 프로세스가 OOM(Out of Memory) 킬러에 의해 종료될 수 있습니다.

## 2) 컨테이너 CPU 제한

### CPU 비율 제한

1. --cpu-shares 옵션

• **`--cpu-shares`**: 컨테이너에 CPU를 1개씩 할당하지 않고 시스템에 존재하는 CPU를 얼마나 나눠 쓸지 명시하는 옵션입니다.
• 값의 비율에 따라 점유율이 달라집니다.
• 기본값은 1024이며, 상대적인 가중치로 작동합니다.

2. 사용 예시

```shell
# 기본 CPU 비율 (1024)
docker run --cpu-shares 1024 nginx

# 절반 비율 (512)
docker run --cpu-shares 512 nginx

# 두 배 비율 (2048)
docker run --cpu-shares 2048 nginx
```

### 특정 CPU 지정

1. --cpuset-cpus 옵션

• **`--cpuset-cpus`**: 호스트에 CPU가 여러 개 있을 경우 특정 컨테이너가 특정 CPU만 사용하도록 설정할 수 있습니다.
• index로 N번째 CPU를 사용할지 지정합니다.

2. 사용 예시

```shell
# 첫 번째 CPU만 사용
docker run --cpuset-cpus 0 nginx

# 첫 번째와 두 번째 CPU 사용
docker run --cpuset-cpus 0,1 nginx

# 0번부터 3번까지 CPU 사용
docker run --cpuset-cpus 0-3 nginx
```

### CPU 스케줄링 제한

1. CFS 스케줄러 설정

• **`--cpu-period`**, **`--cpu-quota`**: 컨테이너의 CFS 주기는 100ms이나, 두 옵션으로 이 주기를 변경할 수 있습니다.

2. 옵션 설명

• **`--cpu-period`**: CFS 스케줄러의 주기를 설정합니다.
• **`--cpu-quota`**: --cpu-period에 설정된 시간 중 CPU 스케줄링에 얼마나 할당할지를 설정합니다.
• period에서 quota를 나눈 값만큼 컨테이너가 CPU의 시간을 할당받습니다.

3. 사용 예시

```shell
# 100ms 주기 중 50ms만 사용 (50% CPU)
docker run --cpu-period 100000 --cpu-quota 50000 nginx

# 100ms 주기 중 200ms 사용 (2개 CPU 상당)
docker run --cpu-period 100000 --cpu-quota 200000 nginx
```

### 직관적 CPU 제한

1. --cpus 옵션

• **`--cpus`**: `--cpu-period`, `--cpu-quota`와 동일하지만 직관적으로 CPU의 개수를 직접 제한할 수 있습니다.
• 소수점으로 비율을 작성할 수 있습니다.

2. 사용 예시

```shell
# 1.5개 CPU 사용
docker run --cpus 1.5 nginx

# 0.5개 CPU 사용 (50% 제한)
docker run --cpus 0.5 nginx

# 2개 CPU 사용
docker run --cpus 2 nginx
```

## 자원 모니터링 및 관리

### 실시간 모니터링

• **`docker stats`**: 실행 중인 모든 컨테이너의 실시간 자원 사용량을 확인할 수 있습니다.
• CPU, 메모리, 네트워크 I/O, 블록 I/O 등의 정보를 제공합니다.

### 자원 제한 조합 사용

```shell
# 메모리와 CPU를 함께 제한
docker run --memory 1g --cpus 1.0 --name limited-container nginx

# 모든 자원을 세밀하게 제한
docker run \
  --memory 512m \
  --memory-swap 1g \
  --cpus 0.5 \
  --cpuset-cpus 0,1 \
  --name resource-limited \
  nginx
```

### 운영 환경에서의 고려사항

• **성능 테스트**: 자원 제한 설정 후 애플리케이션 성능을 충분히 테스트해야 합니다.
• **모니터링**: 지속적인 자원 사용량 모니터링을 통해 적절한 제한값을 찾아야 합니다.
• **확장성**: 오토스케일링 환경에서는 자원 제한이 스케일링 트리거에 영향을 줄 수 있습니다.


# 2.3 도커 이미지

## 도커 허브 (Docker Hub)

### 기본 개념

• 도커 허브는 Docker에서 공식적으로 제공하는 이미지 저장소로서, 도커 계정 보유 시 자유롭게 이미지를 올리고 내려받을 수 있습니다.
• `docker create/pull/run` 명령어로 이미지를 내려받을 때 Docker는 Docker Hub에서 이미지를 검색한 이후 내려받습니다.

### 이미지 품질 및 안전성

1. 공식 이미지 확인

• Docker Hub는 누구든지 이미지를 올릴 수 있기 때문에 Official 라벨이 없는 경우 사용법을 찾을 수 없거나 제대로 동작하지 않을 수 있습니다.
• **Official Image**: Docker에서 공식적으로 관리하는 이미지로 보안 업데이트와 품질이 보장됩니다.
• **Verified Publisher**: 신뢰할 수 있는 조직에서 제공하는 이미지입니다.

2. 이미지 검색 및 선택

• **`docker search`** 명령어를 사용하여 직접 이미지를 검색하여 찾을 수도 있습니다.

**docker search 주요 옵션:**
• **`--filter stars=N`**: 최소 N개 이상의 stars를 받은 이미지만 검색
• **`--filter is-official=true`**: 공식 이미지만 검색
• **`--limit N`**: 검색 결과를 N개로 제한
• **`--no-trunc`**: 결과를 자르지 않고 전체 출력

```shell
# 이미지 검색
docker search nginx

# 필터링된 검색 (최소 stars 수)
docker search --filter stars=100 nginx

# 공식 이미지만 검색
docker search --filter is-official=true nginx

# 결과 개수 제한
docker search --limit 5 nginx
```

3. 이미지 선택 시 고려사항

• **Stars**: 이미지의 인기도를 나타냅니다.
• **Downloads**: 다운로드 수를 확인할 수 있습니다.
• **Last Updated**: 최근 업데이트 날짜를 확인하여 활발히 관리되는지 판단합니다.
• **Security Scanning**: 보안 취약점 스캔 결과를 확인합니다.

## 2.3.1 도커 이미지 생성

### docker commit을 통한 이미지 생성

1. 기본 개념

• 컨테이너 내 변경 사항을 생성하고 이를 **`docker commit`** 명령어를 사용하여 컨테이너를 이미지로 변환할 수 있습니다.
• 실행 중이거나 정지된 컨테이너 모두에서 커밋이 가능합니다.

2. 명령어 형식

**`docker commit [OPTIONS] CONTAINER [REPOSITORY:[TAG]]`**

3. 주요 옵션

• **`-a`**: author를 의미하며 이미지의 작성자를 의미하는 메타데이터를 반영합니다.
• **`-m`**: 커밋 메시지를 의미하며 이미지에 포함될 부가 설명을 작성합니다.
• **`-c`**: Dockerfile 명령어를 추가로 적용할 수 있습니다.
• **`-p`**: 커밋하는 동안 컨테이너를 일시 정지합니다.
• **`--change`**: 이미지 생성 시 Dockerfile 명령어를 적용합니다.

### 실용적인 이미지 생성 예시

1. 기본 커밋 과정

```shell
# 1. 베이스 컨테이너 실행
docker run -it --name my-ubuntu ubuntu:24.04

# 2. 컨테이너 내에서 필요한 작업 수행
apt update && apt install -y curl vim git
echo "Custom configuration" > /etc/myapp.conf

# 3. 컨테이너에서 나와서 커밋
docker commit -a "developer@company.com" -m "Added development tools" my-ubuntu my-custom:v1.0
```

2. 버전 관리를 통한 이미지 발전

```shell
# 첫 번째 버전 생성
docker commit -m "Initial setup with basic tools" my-ubuntu test_commit:A

# 추가 작업을 위한 새 컨테이너 생성
docker run -it --name advanced-container test_commit:A

# 컨테이너 내에서 추가 설정
apt install -y nodejs npm python3
npm install -g express

# 두 번째 버전 생성
docker commit -m "Added Node.js and Python" advanced-container test_commit:B
```

3. 고급 커밋 옵션 활용

```shell
# Dockerfile 명령어와 함께 커밋
docker commit -c "EXPOSE 80" -c "CMD ['nginx', '-g', 'daemon off;']" web-container my-nginx:custom

# 작업 디렉토리 설정과 함께 커밋
docker commit -c "WORKDIR /app" -c "ENV NODE_ENV=production" app-container my-app:prod
```

### 이미지 버전 관리 전략

1. 의미있는 태그 사용

```shell
# 버전별 태그
docker commit my-container myapp:v1.0.0
docker commit my-container myapp:v1.0.1

# 환경별 태그
docker commit my-container myapp:dev
docker commit my-container myapp:staging
docker commit my-container myapp:prod

# 날짜 기반 태그
docker commit my-container myapp:$(date +%Y%m%d)
```

2. 태그 관리 명령어

• **`docker tag`** 명령어로 기존 이미지에 새로운 태그를 부여할 수 있습니다.

**docker tag 사용법:**
• **`SOURCE_IMAGE[:TAG]`**: 태그를 부여할 원본 이미지
• **`TARGET_IMAGE[:TAG]`**: 새로 생성할 이미지 태그

```shell
# 이미지에 추가 태그 부여
docker tag myapp:v1.0.0 myapp:latest
docker tag myapp:v1.0.0 myapp:stable

# 태그 목록 확인
docker images myapp
```

## 2.3.2 이미지 구조 이해

### 레이어 구조의 기본 원리

1. 이미지 레이어 확인

• **`docker inspect`** 명령어로 이미지의 정보를 조회하면 이미지를 구성하는 Layer 정보를 알 수 있습니다.
• A라는 이미지로 B라는 이미지를 새로 생성하면, A 이미지의 레이어를 유지하면서 새로운 레이어만 추가됨을 알 수 있습니다.

2. 레이어 누적 과정 상세 예시

**ubuntu:24.04 → test_commit:A → test_commit:B 생성 과정:**

```shell
# 1단계: ubuntu:24.04 베이스 이미지 (Layer 1)
# - 기본 Ubuntu 파일시스템 포함
# - 크기: 약 70MB

docker run -it --name step1 ubuntu:24.04
# 컨테이너 내에서: apt update && apt install curl
docker commit step1 test_commit:A

# 2단계: test_commit:A (Layer 1 + Layer 2)
# - Layer 1: ubuntu:24.04 레이어 (공유)
# - Layer 2: curl 설치로 인한 변경사항 (약 10MB 추가)
# - 총 이미지 크기: 80MB

docker run -it --name step2 test_commit:A
# 컨테이너 내에서: apt install nodejs npm
docker commit step2 test_commit:B

# 3단계: test_commit:B (Layer 1 + Layer 2 + Layer 3)
# - Layer 1: ubuntu:24.04 레이어 (공유)
# - Layer 2: curl 설치 레이어 (공유)
# - Layer 3: Node.js 설치로 인한 변경사항 (약 50MB 추가)
# - 총 이미지 크기: 130MB
```

3. 레이어 구조의 장점

• **저장 공간 효율성**: 동일한 베이스 레이어를 여러 이미지가 공유합니다.
• **빠른 이미지 빌드**: 변경된 레이어만 새로 생성하면 됩니다.
• **네트워크 효율성**: 이미 존재하는 레이어는 다운로드하지 않습니다.

### 이미지 히스토리 및 분석

1. 이미지 히스토리 확인

• **`docker history`** 명령어를 사용하여 이를 좀 더 쉽게 확인할 수 있습니다.
• 해당 명령어로 이미지가 어떤 레이어로 생성되었는지 알 수 있습니다.

**docker history 주요 옵션:**
• **`--no-trunc`**: 명령어나 ID를 자르지 않고 전체 출력
• **`--format`**: 출력 형식을 사용자 정의로 지정
• **`-H`**: 사람이 읽기 쉬운 크기 형식으로 출력 (기본값)
• **`-q`**: 이미지 ID만 출력

```shell
# 이미지 히스토리 확인
docker history test_commit:B

# 더 자세한 정보 포함
docker history --no-trunc test_commit:B

# 크기 정보와 함께 확인
docker history --format "table {{.ID}}\t{{.CreatedBy}}\t{{.Size}}" test_commit:B
```

2. 히스토리 출력 (test_commit:B)

```
IMAGE          CREATED BY                                      SIZE
abc123...      /bin/sh -c apt install nodejs npm              50MB
def456...      /bin/sh -c apt update && apt install curl      10MB  
ghi789...      /bin/sh -c #(nop) ADD file:... in /             70MB
```

### 이미지 삭제 관리

1. 기본 이미지 삭제

• **`docker rmi`** 명령어를 사용하여 이미지를 삭제할 수 있습니다.
• 단, 이미지를 사용 중인 컨테이너가 존재하는 경우 삭제할 수 없습니다.

**docker rmi 주요 옵션:**
• **`-f, --force`**: 강제로 이미지 삭제 (권장하지 않음)
• **`--no-prune`**: 태그가 없는 부모 이미지를 삭제하지 않음

```shell
# 이미지 삭제
docker rmi test_commit:A

# 여러 이미지 동시 삭제
docker rmi ubuntu:20.04 ubuntu:22.04

# 태그만 삭제 (다른 태그가 있는 경우)
docker rmi myapp:old-version
```

2. 강제 삭제의 문제점

• **`-f`** 옵션을 사용하여 이미지를 강제로 삭제할 수 있지만 레이어 파일을 삭제하지 않고 이름만 지우기 때문에 의미가 없습니다.
• 따라서 이미지를 지우려면 컨테이너를 먼저 지우고 나서 이미지를 지우는 순으로 가야 합니다.

```shell
# 올바른 삭제 순서
docker stop my-container
docker rm my-container
docker rmi my-image:tag
```

3. Dangling Image 관리

• 컨테이너가 사용 중인 이미지를 `docker rmi -f`로 삭제하면 이미지의 이름이 `<none>`으로 변경되며 이를 **Dangling Image**라고 합니다.
• **`docker images -f dangling=true`** 명령어로 별도로 확인이 가능합니다.

**docker image prune 주요 옵션:**
• **`-f, --force`**: 확인 없이 자동으로 삭제
• **`-a, --all`**: 사용하지 않는 모든 이미지 삭제
• **`--filter`**: 특정 조건에 맞는 이미지만 삭제

```shell
# Dangling 이미지 확인
docker images -f dangling=true

# Dangling 이미지 정리
docker image prune

# 사용하지 않는 모든 이미지 정리
docker image prune -a

# 특정 기간 이전 이미지 정리
docker image prune --filter "until=72h"
```

### 이미지 크기 최적화

1. 레이어 수 최소화

```shell
# 비효율적인 방법 (여러 레이어 생성)
docker run ubuntu:24.04
apt update
docker commit step1 temp1
apt install curl
docker commit step2 temp2

# 효율적인 방법 (한 번에 처리)
docker run ubuntu:24.04
apt update && apt install curl && apt clean
docker commit optimized-image
```

2. 불필요한 파일 정리

```shell
# 패키지 캐시 정리와 함께 설치
RUN apt update && apt install -y curl && apt clean && rm -rf /var/lib/apt/lists/*

# 임시 파일 정리
RUN wget http://example.com/file.tar.gz && \
    tar -xzf file.tar.gz && \
    rm file.tar.gz
```

## 2.3.3 이미지 추출

### docker save/load를 통한 이미지 백업

1. 이미지 추출 (save)

• **`docker save`** 명령어로 컨테이너의 커맨드, 이미지 이름 및 태그 등 모든 메타데이터를 하나의 파일로 추출할 수 있습니다.
• **`-o`** 옵션에는 추출할 파일명을 입력합니다.

**docker save 주요 옵션:**
• **`-o, --output`**: 출력 파일 경로 지정
• 여러 이미지를 동시에 지정 가능

```shell
# 단일 이미지 추출
docker save -o ubuntu-24.04.tar ubuntu:24.04

# 여러 이미지 동시 추출
docker save -o my-images.tar ubuntu:24.04 nginx:latest mysql:8.0

# 압축과 함께 추출
docker save ubuntu:24.04 | gzip > ubuntu-24.04.tar.gz

# 특정 태그만 추출
docker save -o test-images.tar test_commit:A test_commit:B
```

2. 이미지 복원 (load)

• **`docker load`** 명령어로 추출된 이미지를 도커에 다시 로드할 수 있습니다.
• 이미지의 모든 메타데이터를 `save`로 저장했기에 `load`로 이를 불러오면 이전의 이미지와 완전히 동일한 이미지가 엔진에 생성됩니다.

**docker load 주요 옵션:**
• **`-i, --input`**: 입력 파일 경로 지정
• **`-q, --quiet`**: 로드 과정에서 출력 최소화

```shell
# 이미지 파일 로드
docker load -i ubuntu-24.04.tar

# 압축된 이미지 로드
gunzip -c ubuntu-24.04.tar.gz | docker load

# 표준 입력을 통한 로드
cat my-images.tar | docker load
```

### docker export/import의 차이점

1. export/import 특징

• **`docker export`**와 **`docker import`** 명령어도 save/load처럼 유사한 기능을 하지만 export 모드는 컨테이너의 파일 시스템을 tar로 추출하여 컨테이너 및 이미지의 설정 정보를 저장하지 않습니다.

2. 사용 방법 및 차이점

**docker export 주요 옵션:**
• **`-o, --output`**: 출력 파일 경로 지정

**docker import 주요 옵션:**
• **`-c, --change`**: import 시 Dockerfile 명령어 적용
• **`-m, --message`**: import 시 커밋 메시지 설정

```shell
# export: 컨테이너의 파일시스템만 추출
docker export my-container > container-filesystem.tar
# 또는
docker export -o container-filesystem.tar my-container

# import: 파일시스템을 새로운 이미지로 생성
cat container-filesystem.tar | docker import - my-new-image:latest

# 설정과 함께 import
docker import --change "ENV DEBUG=true" --change "EXPOSE 8080" container-filesystem.tar my-app:v1.0
```

3. save/load vs export/import 비교

| 구분 | save/load | export/import |
|------|-----------|---------------|
| 대상 | 이미지 | 컨테이너 |
| 메타데이터 보존 | O (완전 보존) | X (일부 손실) |
| 레이어 구조 | 보존됨 | 단일 레이어로 병합 |
| 이미지 히스토리 | 보존됨 | 손실됨 |
| 파일 크기 | 상대적으로 큼 | 상대적으로 작음 |
| 용도 | 이미지 백업/배포 | 컨테이너 스냅샷 |


