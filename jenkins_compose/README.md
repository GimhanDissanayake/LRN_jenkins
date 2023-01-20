### Why docker-compose?
1. need to run more than one container (K8s complexity, don’t need Kubernetes’ resiliency and scaling.)
2. defining how to run multiple containers in a single configuration file and start, stop, and restart them with a single command.

## can run a Jenkins controller with a single Docker command:
```
$ docker run -it -p 8080:8080 jenkins/jenkins:lts
```
*** If restarted > lose all data > need to set up a volume

## docker-compose.yml
```
# docker-compose.yaml
version: '3.3'
services:
  jenkins:
    image: jenkins/jenkins:lts
    privileged: true
    user: root
    ports:
      - 8080:8080   # web UI
      - 50000:50000   # to communicate with  Jenkins agents on other machines
    container_name: jenkins
    volumes:
      - /home/${myname}/jenkins_compose/jenkins_configuration:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
```

## Run Jenkins Controller
```
docker-compose up -d

# Browse http://[IP]:8080

# Get password from logs
docker logs jenkins | less
```

## Adding a Jenkins Agent With Docker Compose

### controller access the agent via SSH
```
# generate an SSH key
ssh-keygen -t rsa -f jenkins_agent

# Manage Jenkins >> Manage Credentials >> Jenkins(Global) >> Add Credentials
1. Select SSH Username with private key.
2. Limit the scope to System. This means the key can’t be used for jobs.
3. Give the credential an ID.
4. Provide a description.
5. Enter jenkins for a username. Don’t use the username used to create the key.
6. Under Private Key,  check Enter directly.
```

## Update docker-compose.yaml
```
# docker-compose.yaml
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    privileged: true
    user: root
    ports:
      - 8080:8080
      - 50000:50000
  container_name: jenkins
  volumes:
    - /home/${myname}/jenkins_compose/jenkins_configuration:/var/jenkins_home
    - /var/run/docker.sock:/var/run/docker.sock
agent:
  image: jenkins/ssh-agent:jdk11
  privileged: true
  user: root
  container_name: agent
  expose:
    - 22
  environment:
    - JENKINS_AGENT_SSH_PUBKEY=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDrSF2XICucxX//hz0PVgDVv3EisY6C99u9sA1QoZSdHuXW/H5i/1BT3CtUzBAsCOJxdSuadKfJZnNDEL98lmagU79tAgNjVgCnUyoKEkaEOz4J416cUsF/hR/rKUeRimcox6BncwixYsfmmcZMo1ImKWqdYIDu6TJV0RjhdpZyfn1RTxV0VXP1cn5yLO4aGz8ZCPLmvxVONykc0FLBVz3XgbpFW9xY3O4iaSrpoYn9Ce0m+Txx0lPYlm5bt6C2F6hp0LaBACsekAZz0oAXCKAel9gj27l8vXNj0eN2J8fxQdsTDtZ6Pnnd73ATfK114ceLDHL2VyV8m1KxD5Cm+zFgB7AfZ0xBsN+KGBtPqha7971lZSUvjKvRNXnPk3xrzwTPGKL8vnJOUidsx+6KzSTPB/sk8L9YsEy9i023KZTQMXTv0IrLp8Bbm3V4wWGsdfmviLMq3x2QMSMXlpE00alCar2eSah0425POSpyEtLFEDj2tURVjP+LmKroc8bOILc= egoebelbecker@zaku
```

## Run Jenkins Controller
```
docker-compose up -d

# Browse http://[IP]:8080

Manage Jenkins >> Manage Nodes and Clouds >> New Node >> 

Name: Jenkins_agent
Root Dir: /home/jenkins/agent
Usage: User this node as much as possible
Launch method: via SSH
Java Path: /opt/java/openjdk/bin/java

SAVE >> logs >> ***Agent successfully connected and online
```