## 1. 베이스 만들기
먼저 기본이 되는 베이스 OS를 다운로드 받습니다.  
보통은 server의 설치가 되어있는 OS를 따라 설치하게 되는데, 이렇게 하면 기존의 설치되어 있는 리눅스 시스템설정을 쓸 수 있다고 합니다.  
예제에서는 CentOS를 기본으로 하도록 하겠습니다. 먼저 docker search를 통해 OS를 확인하고 다운로드 합니다.  
```{.text}
$ docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   4995                [OK]
ansible/centos7-ansible            Ansible on Centos7                              119                                     [OK]
jdeathe/centos-ssh                 CentOS-6 6.10 x86_64 / CentOS-7 7.5.1804 x86…   102                                     [OK]
consol/centos-xfce-vnc             Centos container with "headless" VNC session…   71                                      [OK]
imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              48                                      [OK]
.
.
.
docker pull centos:latest <=== centos를 다운로드.
```

## 2. hadoop 구성을 위한 설정
하둡의 기본 구성을 하기 위해서는 기본적으로 설치 되어야 하는 것들이 있습니다. 아래의 내용은 기본적인 값들을 다운로드 합니다.
```{.text}
$ yum update <=== yum 최신버전으로 업그레이드
$ yum list java*jdk-devel <=== 자바 버전 확인
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Available Packages
java-1.6.0-openjdk-devel.x86_64                                                         1:1.6.0.41-1.13.13.1.el7_3                                                          base
java-1.7.0-openjdk-devel.x86_64                                                         1:1.7.0.191-2.6.15.4.el7_5                                                          updates
java-1.8.0-openjdk-devel.i686                                                           1:1.8.0.191.b12-0.el7_5                                                             updates
java-1.8.0-openjdk-devel.x86_64                                                         1:1.8.0.191.b12-0.el7_5                                                             updates

$ yum install java-1.8.0-openjdk-devel.x86_64 <=== openJDK 다운로드

$ yum install date
```
위 내용은 yum을 통해 받을 수 있는 기본적인 데이터 입니다. 이후 zookeeper와 hadoop2.7.7 버전을 다운로드 해 줍니다.
```{.text}
$ wget http://apache.mirror.cdnetworks.com/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz <=== zookeeper 설치
$ tar -zvf zookeeper-3.4.10.tar.gz
$ mv zookeeper-3.4.10 /usr/local/ && ln -s /usr/local/zookeeper-3.4.10 /usr/local/zookeeper
 
# wget https://dist.apache.org/repos/dist/release/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz <=== hadoop 설치
# tar -zvf hadoop-2.7.7.tar.gz
# mv hadoop-2.7.7 /usr/local/ && ln -s /usr/local/hadoop-2.7.7 /usr/local/hadoop
```
위 내용의 설치가 끝나면 "~/.bashrc"에 들어가 설치된 java, hadoop, zookeeper 등의 설정을 해줍니다.
```{.text}
.
.
.
# JAVA SETTING
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-0.el7_5.x86_64
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH

# HADOOP SETTING
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
export HADOOP_PREFIX=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_PREFIX
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_PREFIX/lib/native
export HADOOP_CONF_DIR=$HADOOP_PREFIX/etc/hadoop
export HADOOP_HDFS_HOME=$HADOOP_PREFIX
export HADOOP_MAPRED_HOME=$HADOOP_PREFIX
export YARN_HOME=$HADOOP_PREFIX

# ZOOKEEPER SETTING
export ZOOKEEPER_HOME=/usr/local/zookeeper
export ZOOKEEPER_PREFIX=$ZOOKEEPER_HOME
export ZOO_LOG_DIR=$ZOOKEEPER_HOME/logs

export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEEPER_HOME/bin
```

## 3. 네트워크 설정: generate the SSH rsa key 생성(서버간의 통신이 가능하게 공개키를 설정)
