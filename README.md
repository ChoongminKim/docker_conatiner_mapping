# docker_container_mapping
docker conatiner 생성시 Host pc USER 계정 생성 및 UID, GID Mapping

## Dockfile
* Docker Container 초기 부팅 시 root와 user 비밀번호는 ID와 같도록 설정
* **필요시 변경해서 사용**
```txt
# Docker Image와 Tag 설정
FROM [IMAGE:TAG] 

ARG USER=$USER
ARG UID=$UID

RUN apt-get update && apt-get install -y sudo vim
RUN groupadd -g ${UID} ${USER}
RUN useradd ${USER} -g ${USER} -u ${UID} -s /bin/bash
RUN usermod -aG sudo ${USER}

# Docker Container 내에서 root와 user 비밀번호 설정
# ex) echo "사용자 아이디:비밀번호" | chpasswd
RUN echo "root:root" | chpasswd
RUN echo "${USER}:${USER}" | chpasswd

# sudo 실행시 $USER PATH 환경 유지하도록 설정
RUN sed -i "11s/Defaults/#Defaults/" /etc/sudoers
RUN echo "Defaults	env_keep=PATH" >> /etc/sudoers

USER ${USER}
```

## Dockfile build
```bash
$ docker build --build-arg UID=$UID --build-arg USER=$USER -t <IMAGE NAME>:<TAG> .
```

## Docker run
```bash
$ docker run -d -it -v $HOME:$HOME -v /public:/public -p <PORT NUMBER>:8888 --gpus "device=<DEVICE NUMBER>" --name <CONTAINER NAME> <IMAGE NAME>:<TAG>
```
