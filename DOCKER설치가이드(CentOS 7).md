## CentOS 7 DOCKER 설치가이드
***docker는 CE(Community Edition)*** 와 ***EE(Enterprise Edition)*** 두개가 존재합니다.

***Docker Community Edition(CE)*** 는 무료로 누구나 사용할 수 있습니다.  
Docker CE는 오픈 소스로서, Windows, Mac 및 Linux를 비롯한 다양한 플랫폼에서 사용할 수 있습니다.  
***Docker Enterprise Edition(EE)*** 에는 워크플로 관리 및 보안을 위한 기능이 추가되어 있으며 유료로 사용할 수 있습니다.  
일단 컨테이너 기반의 앱을 실험하고 소규모 구성을 목적으로 하고 있기 때문에 무료버전인 CE를 사용하도록 하겠습니다.  

### Step1> docker(CE) 설치하기 : docker repository 설정하기
1. 먼저 처음 설치하는 host machine이라면, docker repository 설치를 해야합니다. 그래야 install과 update를 할 수 있습니다.  
yum-utils는 yum-config-manager 유틸리티를 제공하고,  
device-mapper-persistent-data 및 lvm2는 devicemapper 저장 장치 드라이버에 필요합니다.
```{.text}
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```  

2. 안정적인 저장소를 설정하려면 다음 명령을 사용해야 합니다.  
edge 또는 test repository에서 빌드를 설치하려는 경우에도 항상 안정적인 저장소가 필요합니다.
```{.text}
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```  

3. Optional: edge와 test repository의 enable하는 방법 입니다. **ocker.repo** 파일에 기본적으로 disabled로 정의 됩니다.  
```{.text}
$ sudo yum-config-manager --enable docker-ce-edge
$ sudo yum-config-manager --enable docker-ce-test
```
**--disable** flag를 실행중인 yum-config-manager 명령어와 함께  
edge 또는 test repository disable 할 수 있습니다.
```{.text}
$ sudo yum-config-manager --disable docker-ce-edge
```

### Step2> docker(CE) 설치하기 : Install docker CE(Community Edition)
1. 아래의 명령어를 통해 Docker CE의 최신 버전을 설치하거나 특정 버전을 설치가 가능합니다.
```{.text}
$ sudo yum-config-manager --disable docker-ce-edge
```
GPG 키를 수락할지 묻는 메시지가 나타나면 지문이 일치하는지 확인해야 합니다.  
*** 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35 ***  
docker를 installed 하고 바로 실행되지 않습니다. docker의 group은 생성되었지만, 사용자가 추가 되지 않았기 때문입니다.  

2. Docker CE의 특정 버전을 설치하려면 사용 가능한 버전을 repo에 나열한 다음 선택하여 설치하세요.
```{.text}
$ yum list docker-ce --showduplicates | sort -r
docker-ce.x86_64         3:18.09.0-3.el7                        docker-ce-test
docker-ce.x86_64         3:18.09.0-3.el7                        docker-ce-stable
docker-ce.x86_64         3:18.09.0-3.el7                        docker-ce-edge
docker-ce.x86_64         3:18.09.0-3.el7                        @docker-ce-edge
docker-ce.x86_64         3:18.09.0-2.1.rc1.el7                  docker-ce-test
docker-ce.x86_64         3:18.09.0-1.5.beta5.el7                docker-ce-test
docker-ce.x86_64         3:18.09.0-1.3.beta3.el7                docker-ce-test
.
.
.
```
sort 를 사용하면, 위 내용처럼 최신 순을 보여주며, 위 해당 버전을 골라 아래와 같이 설치하면 됩니다.
```{.text}
$ sudo yum install docker-ce-<VERSION STRING>
```

3. Docker를 실행합니다.  
```{.text}
$ sudo systemctl start docker
```

4. ***hello-world*** 이미지를 통해 docker가 올바르게 설칙가 완료 되었는지를 확인합니다.
```{.text}
$ sudo docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 
[root@localhost ~]# docker ps -al
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
ef56243f8dd8        hello-world         "/hello"            17 seconds ago      Exited (0) 16 seconds ago                       distracted_cori
```

### Step3> docker(CE) 제거하기 : Uninstall Docker CE(Community Edition)
1. Docker package 제거하기
```{.text}
$ sudo yum remove docker-ce
```

2. 라이브러리 삭제하기
Images, containers, volumes, or customized configuration 파일들은 자동으로 지워지지 않습니다.  
아래의 명령어를 통해 삭제해야합니다.
```{.text}
$ sudo rm -rf /var/lib/docker
```
