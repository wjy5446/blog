---
title: 'Docker 간단 정리 - 볼륨 및 네트워크'
date: 2020-10-25 16:21:13
category: 'docker'
draft: false
---

```
시작하세요. 도커 책을 읽고 정리합니다.
```

# 도커 볼륨
- 컨테이너 내부에서 파일을 만들고 삭제하면, 내부 파일도 삭제된다. 그래서 네트워크의 데이터를 보존하기 위해서 볼륨을 사용한다.

1. 호스트 볼륨 공유
- docker run의 `-v`을 이용해서 볼륨을 공유한다.
- 호스트 디렉토리는 컨테이너 디렉토리에 마운트한다.
```
docker run -d -v [host의 볼륨 위치]:[컨테이너 볼륨 위치]
```

2. 볼륨 컨테이너 
- 볼륨을 사용하는 컨테이너를 다른 컨테이너와 공유 한다.
- docker run에서 `--volumes-from`으로 볼륨 컨테이너 공유

```
docker run -it --volumes-from [볼륨 컨테이너 이름] [이미지 이름]
```

3. 도커 볼륨
- 도커 자체에서 제공하는 볼륨 기능을 사용한다.
- 파일은 실제 호스트 어딘가에 저장되어 있음. `docker inspect --type volume [도커 볼륨 이름]`으로 해당 위치 확인 가능

- `docker volume create --name [도커 볼륨 이름]`: 도커 볼륨 생성

- `-v [볼륨 이름]:[컨테이너 공유 디렉토리]` 옵션으로 연결
```
docker run -i -t -v [도커 볼륨 이름]:[컨테이너 공유 디렉토리] [이미지 이름]
```

- `docker volume prune`으로 사용되지 않은 볼륨 삭제

# 도커 네트워크

- 도커는 컨테이너에 IP를 순차적으로 지정하고 호스트 내부에 veth라는 네트워크 인터페이스를 생성하여 연결
- 또한 docker0라는 veth와 eth0를 연결해주는 역활을 하는 브리지를 생성한다.
`컨테이너 eth -> veth => docker0 브리지 -> 호스트 eth0`

## 도커 네트워크 기능

1. 브리지 네트워크 
- 자동 브리지 말고 사용자 정의 브리지를 생성하여 연결 가능

- `docker network create --driver [브리지 이름]` : 브리지 생성
- `docker run -i -t --net [브리지 이름] [이미지 이름]` : 해당 브리지를 사용하는 컨테이너 생성

2. 호스트 네트워크
- 호스트의 네트워크를 그대로 사용
- `docker run -i -t --net host [이미지 이름]`

3. 논 네트워크
- 네트워크를 사용하지 않음
- `docker run -i -t --net none [이미지 이름]`

4. 다른 컨테이너와 네트워크 공유
- 다른 컨테이너의 네트워크를 공유한다.
- `docker run -i -t --net container:[컨테이너 이름] [이미지 이름]`

## 도커 DNS
- 도커 내부에는 DNS가 존재
- `docker run -i -t -d --net mybridge --net-alias [net 이름]`: `--net-alias`를 통해서 도커 DNS 설정 가능