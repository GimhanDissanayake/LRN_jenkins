# LRN_jenkins

## Intro
1. Jenkins is an automation server.
2. often associated with building source code and deploying the results
3. ability to distribute jobs across multiple nodes
4. Jenkins controller sends jobs to the appropriate agent based on the job requirements and the resources available at the time.
5. possible to run jobs on the controller, Best practice is to always create at least one agent and run jobs there

## can run a Jenkins controller with a single Docker command:
```
$ docker run -it -p 8080:8080 jenkins/jenkins:lts
```
*** If restarted > lose all data > need to set up a volume

## docker-compose.yml
```
# docker-compose.yaml
version: '3.8'
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

