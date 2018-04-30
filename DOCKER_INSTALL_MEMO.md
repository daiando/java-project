### 1. preparing

- setup docker on jenkins slave
- remove current docker package
- install yum utils
- add yum repo for docker
- yum-utils provides the yum-config-manager utility

```
yum remove docker docker-common container-selinux
yum install -y yum-utils
yum install -y device-mapper-persistent-data \
  lvm2
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo
yum install docker-ce
```


### 2. confirm

- add jenkins user to docker group

```
gpasswd -a jenkins docker
```
