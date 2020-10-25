---
title: 'Docker 간단 정리 - 기초 및 주요 명령어'
date: 2020-10-25 16:21:13
category: 'docker'
draft: false
---

```
시작하세요. 도커 책을 읽고 정리합니다.
```

# 도커란 

- 기존 가상 머신 방식 : 하이퍼 바이저에 의해 완전히 독립된 공간과 자원을 할당 받은 Guest OS 가상으로 생성 
  - 단점 : 일반 호스트에 비해 성능 손실 존재, 이미지가 크기 때문에 배포가 힘듬.

- 도커 : 리눅스 자체 기능인 chroot, namespace, cgroup을 활용해 프로세스 단위의 격리 환경을 생성, 호스트 커널은 공유해서 사용
  - 장점 : 배포가 빠르며, 성능 손실이 거의 없음.

# 컨테이너와 이미지

- 이미지 : 컨테이너를 생성을 위한 여러개 레이어로 구성된 바이너리 파일, 이름은 `[저장소 이름]/[이미지 이름]:[태그]`

- 컨테이너 : 이미지로 생성되어 독립된 파일시스템, 시스템 자원, 네트워크를 할당 받은 것.

# 주요 Docker 명령어
- `docker images`: 이미지 리스트 확인
- `docker ps`: 컨테이너 리스트 확인
- `docker rename`: 컨테이너 이름 변경하는 명령어
- `docker inspect []`: 컨테이너 정보 확인하는 명령어

- `docker pull`: 원격 저장소에서 이미지를 받는 명령어
- `docker create`: 이미지에서 컨테이너를 생성하는 명령어
- `docker start`: 생성된 컨테이너를 시작하는 명령어
- `docker attach`: 시작된 컨테이너에 접속하는 명령어

- `docker run`: `docker pull` + `docker create` + `docker start` + `docker attach`
- `docker stop`: 컨테이너 종료하는 명령어

- `docker rm [컨테이너 이름]`: 컨테이너 삭제하는 명령어
- `docker rmi [이미지 이름]`: 이미지 삭제하는 명령어

- `docker exec -i -t [컨테이너 이름] [명령어]`: 컨테이너 내부에 명령어 입력하는 명령어

# Docker run 명령어 옵션

- `docker run -i -t -p 3306:3306 --name server -e PASSWORD=password --link worddb:mysql server`

- `-i -t`: 표준 입출력이 상호작용이 가능한 쉘 환경을 사용하여 container 내부로 들어갈 수 있도록 설정
- `-d`: detach 모드로 컨테이너가 백그라운드로 실행하도록 설정, container에는 반드시 foreground 실행되어야 한다. 없으면 컨테이너 종료
- `-p`: port 바인딩 설정 `[호스트 포트]:[컨테이너 포트]` 이러게 설정
- `-e`: 환경변수 설정
- `--name`: 컨테이너의 이름을 설정
- `--link`: 해당 컨테이너의 네트워크에 연결하는 설정


