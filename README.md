[![CircleCI](https://circleci.com/gh/netas-ankara/poc-docker-ci-py/tree/master.svg?style=shield)](https://circleci.com/gh/netas-ankara/poc-docker-ci-py/tree/master)
# poc-docker-ci-py
This POC is in search of the continuous integration environment with the ultimate container technology Docker in a Python ecosystem.

Just commit a change and see the build process from this link :
https://circleci.com/gh/netas-ankara/poc-docker-ci-py/tree/master

All changes result in a push to our docker hub. You can track it from this link : https://hub.docker.com/r/netasankara/poc-docker-ci-py/tags/

The configuration for the ci process is as follows 

```
version: 2  -> This is the circle ci yml version
jobs:
  build:
    working_directory: /dockerapp -> The directory to run the steps
    docker:
      - image: docker:17.05.0-ce-git -> This is the docker version in which 
      all of the processes will be executed
    steps:
      - checkout -> checkout the source to the working directory
      - setup_remote_docker -> When setup_remote_docker executes, a remote environment will be created, 
      and your current primary container will be configured to use it. 
      Then, any docker-related commands you use will be safely executed in this new environment.
      - run: -> Just a step named Install dependencies
          name: Install dependencies
          command: |
            apk add --no-cache py-pip=9.0.0-r1
            pip install docker-compose==1.15.0
      - run: -> Start the application and run the tests.
          name: Run tests
          command: |
            docker-compose up -d
            docker-compose run dockerapp python test.py
      - deploy: -> Push the image to our docker hub tagged with commit hash.
          name: Push application Docker image
          command: |
            docker login -e $DOCKER_HUB_EMAIL -u $DOCKER_HUB_USER_ID -p $DOCKER_HUB_PWD
            docker tag dockerapp_dockerapp netasankara/poc-docker-ci-py:$CIRCLE_SHA1
            docker tag dockerapp_dockerapp netasankara/poc-docker-ci-py:latest
            docker push netasankara/poc-docker-ci-py:$CIRCLE_SHA1
```