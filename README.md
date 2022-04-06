# docker-compose构建远程桌面kali

### 配置用户名密码
打开项目根目录下的 .env 环境变量配置文件，一定记得修改为自己的复杂密码，默认是很弱的。
```bash
KALI_USER=kali
KALI_PASSWORD=kali@123
```
### 构建镜像文件
```bash
# 基于 kali 官方镜像 rolling 版
FROM kalilinux/kali-rolling  

# 引入环境变量
ARG KALI_USER
ARG KALI_PASSWORD
env KALI_USER=${KALI_USER} \
    KALI_PASSWROD=${KALI_PASSWORD}

# 安装 kali 基础环境，选取 kali-linux-large
RUN apt-get -y update && apt-get -y upgrade && \
   DEBIAN_FRONTEND=noninteractive apt-get install -y \
   pciutils \
   bash-completion && \
   apt-get autoremove -y && \
   apt-get clean

RUN printf "alias ll='ls $LS_OPTIONS -l'\nalias l='ls $LS_OPTIONS -lA'\n\n# enable bash completion in interactive shells\nif [ -f /etc/bash_completion ] && ! shopt -oq posix; then\n    . /etc/bash_completion\nfi\n" > /root/.bashrc

# 待挂载工作目录，将它映射到宿主机，常用临时工具或者文件放这里
RUN mkdir /data

# 安装远程桌面环境 kali-desktop-xfce、远程桌面服务 xrdp
RUN apt-get -y install xorg xrdp iputils-ping
RUN useradd -ms /bin/bash $KALI_USER
RUN echo '$KALI_USER:$KALI_PASSWORD' |chpasswd
RUN echo "$KALI_USER    ALL=(ALL) ALL" >> /etc/sudoers

# 对外暴露 3389 端口
EXPOSE 3389
VOLUME /data /root /home/$KALI_USER
ENTRYPOINT /etc/init.d/xrdp start && tail -F /var/log/xrdp-kali.log
```

### 一键部署
机器预先安装好 docker 和 docker-compose ，解压项目文件，按照上文配置后执行指令即可。
```bash
$ git clone https://github.com/guan4tou2/kali_on_docker.git
$ cd kali_on_docker
$ chmod 755 setup.sh
$ docker-compose up -d
```

