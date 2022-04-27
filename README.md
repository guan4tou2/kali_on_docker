# docker-compose构建远程桌面kali

### 配置用户名密码
打开项目根目录下的 .env 环境变量配置文件，一定记得修改为自己的复杂密码，默认是很弱的。
```bash
KALI_USER=kali
KALI_PASSWORD=kali@123!
```

### 一键部署
机器预先安装好 docker 和 docker-compose ，解压项目文件，按照上文配置后执行指令即可。
```bash
$ git clone https://github.com/guan4tou2/kali_on_docker.git
$ cd kali_on_docker
$ docker-compose up -d
#$ docker exec -it `docker ps -qf "name=kali_on_docker"` /bin/bash
#root@kali:/# ./setup.sh
```
setup.sh安裝kali桌面與套件。

