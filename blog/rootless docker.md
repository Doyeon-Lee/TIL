## rootless 설정

https://docs.docker.com/engine/security/rootless/

> rootless docker는 일반 docker를 먼저 설치하고 rootless 설정을 활성화하면 된다.

<br/>

### 기존 서비스 비활성화

기존에 docker daemon이 실행 중이라면 root 계정으로 다음 명령어를 실행하여 비활성화한다. shutdown/disable을 하지 않는 이상은 도커는 계속해서 rootful로 돌고 있을 것이다. 만약 안된다면 --force 옵션을 사용하라.

- docker.sock: 도커 데몬과 클라이언트가 통신하기 위해 사용되는 소켓 파일. 기본적으로 도커 데몬 실행 시 생성된다.

```bash
systemctl disable --now docker.service docker.socket
rm /var/run/docker.sock
```

<br/>

### docker 서비스 재설치

만약 도커 20.0 버전 이상을 rpm 파일로 설치했다면, `/usr/bin/dockerd-rootless-setuptools.sh` 파일이 생성되어 있을 것이다. (없다면 docker-ce-rootless-extras 패키지 설치가 필요하다) root가 아닌 일반 계정으로 다음 파일을 실행하여 daemon을 설정한다. 만약 해당 서비스를 제거하고 싶다면 uninstall을 실행하면 된다.

```bash
/usr/bin/dockerd-rootless-setuptool.sh install
/usr/bin/dockerd-rootless-setuptool.sh uninstall
```

<br/>

### docker 실행

rootless docker 모드에서는 기본적으로 사용자 계정에 해당하는 `~/.config/systemd/user/docker.service` 파일에 시스템 서비스가 설치된다. `--user` 옵션을 통해 해당 계정이 도커 데몬의 라이프 사이클을 관리하도록 설정하면 도커 데몬이 root가 아닌 사용자 세션에서 실행될 수 있다.

- docker.service: 도커 데몬을 관리하는 systemd 서비스 유닛 파일. 이 파일에는 도커 데몬을 시작, 중지, 재시작, 상태 점검 등의 작업을 제어하는 명령어와 설정이 포함되어 있음
- linger 모드 활성화: 해당 명령은 사용자가 로그아웃한 후에도 해당 사용자의 systemd 서비스를 계속 실행하도록 설정하는 옵션이다.

```bash
systemctl --user start docker  # 도커 데몬을 현재 세션에서 즉시 시작
systemctl --user enable docker  # 시스템 시작 시 도커 데몬을 자동으로 시작
loginctl enable-linger [일반계정ID]
```

<br/>

### Client 환경 변수 설정

rootless 모드로 변경됨에 따라 사용하는 소켓 파일의 위치도 변경되었다. 해당 환경변수 값을 수정한 뒤 echo를 통해 변경된 값을 확인할 수 있다. 이 경로는 위에서 rootless-setup 설치 시 디폴트로 설정된 위치이다.

- 소켓 파일 경로
  - 디폴트 경로: `$XDG_RUNTIME_DIR/docker.sock`
  - `$XDG_RUNTIME_DIR`는 사용자별 런타임 디렉토리를 지정하는 환경 변수로, 일반적으로 `/run/user/$UID`로 설정된다. 여기서 `unix://`는 실제 파일 경로가 아니라 docker가 사용하는 소켓의 URI 형식을 나타낸다.
- 데이터 디렉토리
  - 디폴트 경로: `~/.local/share/docker`
  - docker 이미지, 컨테이너 데이터, 볼륨 등이 저장되는 경로. NFS(Network File System) 상에 위치하면 성능 문제가 발생할 수도 있고, docker와 같은 데몬이 파일 시스템에 빠르게 액세스해야 하는 경우에는 적합하지 않다.
- 데몬 설정 디렉토리
  - 디폴트 경로: `~/.config/docker`
  - 도커 데몬의 설정 파일을 관리하는 경로로, `~/.docker`와는 다르다는 점에 주의해야 한다. 이 곳에는 client가 사용하는 설정과 인증 정보를 저장한다.

```bash
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock
echo $DOCKER_HOST
```

<br />

### docker GPU 관련 설정

```bash
nvidia-ctk runtime configure --runtime=docker --cinfog=$HOME/.config/docker/daemon.json
systemctl --user restart docker
nvidia-ctk config --set nvidia-container-cli.no-groups --in-place
```
