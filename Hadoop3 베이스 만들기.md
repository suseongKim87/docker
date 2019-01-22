## 1. 베이스 만들기
docker의 이미지를 만들기 위해서 최소한의 os image를 다운로드 받아야 합니다. (centos, ubuntu etc...)
예제에서는 centos를 기반으로 하고 있기 때문에 centos 7 을 기반으로 하도록 하겠습니다.
사실 centos에서 docker project를 하기위해서 버전업 까지 하게되었습니다... linux 서버 설치 관련해서는 다음에 작성하도록 하겠습니다.

먼저 centos 7의 기본이 되는 docker hadoop base image를 만들기 위해서는 먼저 centos image를 다운 받아야 합니다.

#### 1) centOS 7 이미지 다운로드

```{.text}
docker hub에 있는 centos 관련 이미지를 확인해 볼 수 있습니다.
$ docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   5133                [OK]
ansible/centos7-ansible            Ansible on Centos7                              119                                     [OK]
...
```
아래와 같이 다운로드를 할 경우, CentOS의 offcial image를 다운로드 합니다. 
```{.text}
$ docker pull centos
```

