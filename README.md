# for IP in docker primary bridge network

since we couldn't use dns feature of docker-compose in
primary network. so I used depends on to re-arrange the boot-order, which can give me desired ip address in each
docker container.

if we want to use the gitlab in jenkins server, for the ssh://
address of git repo, you need to specify the docker0's address
to get touch with the gitlab server.

## jenkins part
```bash
cat /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com"
  ],
  "insecure-registries" : ["http://${public_ip}:30002"],
  "max-concurrent-downloads": 10,
  "log-driver": "json-file",
  "log-level": "warn",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
    },
  "data-root": "/var/lib/docker"
}
sudo systemctl reload docker.service
# build docker image
cd jenkins && docker build -f Dockerfile_jenkins -t jenkins:test .
docker tag jenkins:test ${public_ip}:30002/library/jenkins:test
docker login http://${public_ip}:30002
docker push ${public_ip}:30002/library/jenkins:test
curl -L https://get.daocloud.io/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

```
