---
tags: [Git]
title: Gitlab CI / CD Notes
created: '2020-09-01T13:07:05.591Z'
modified: '2020-09-01T18:11:49.522Z'
---

# Gitlab CI / CD Notes

## Initializing a basic CI / CD Process

To start using the gitlab CI / CD Process you need to add a file in the root directory of your repo named `.gitlab-ci.yml`. On any push to your repository GitLab will look for the .gitlab-ci.yml file and start jobs on runners according to the contents of the file, for that commit.

## Formatting a gitlab-ci.yml file.  

An example file might look something like this,

```
stages:
  - Test
  - Build

run tests:
  stage: Test
  image: "ubuntu:18.04"
  script: 
    - test1.sh
    - test2.sh

build app:
  stage: Build
  image: "ubuntu:18.04
  script:
    - build.sh
```

## Stages
Stages consist of a series of jobs that your code must go thru.  You can specify any number of stages with any name you like.  Stages are ran one at a time in the order you list them.  If one fails the pipeline will halt and not run the remaining stages.  In the above example we are asking gitlab to run two stages named Test and Build.  

## Actions

Each stage will need to consist of one or more actions that gitlab needs to perform.  Each action will cause gitlab to create a docker container, copy our code into that container and perform the commands we tell it on our code.  

In the above example we have two actions, one named `run tests` and one named `build app`.  Actions can be named anything. 

The `stage` variable tells gitlab which stage to run the action under.  An action must reference a stage.  
The `image` variable tells gitlab which docker image to run the code in.  This can either be an image from Dockerhub or a private docker repository. In the example above we are using the "ubuntu:18.04" image from dockerhub.  
The `script` variable is a list that tells gitlab to run the following commands in order.  In our `run tests` action we are telling gitlab to run the test1.sh file that is within our code and when that is complete to run the test2.sh file.  If any of the commands return an error the action will be considered failed and the stage will fail. 

## Example

Here is a more complex example of the `gitlab-ci.yml` file

```
stages:
  - Test
  - Build
  - Deploy

flake8 linter:
  stage: Test
  image: "python:3.7-alpine3.10"
  script: 
    - python --version
    - python -m pip install -r requirements.txt
    - flake8 --max-line-length=120 app.py

http test:
  stage: Test
  image: "python:3.7-alpine3.10"
  script:
    - python --version
    - python -m pip install -r requirements.txt
    - apk add curl
    - python app.py &
    - sleep 15
    - curl localhost:80
    - echo "content"
    - exit 0 

build app:
  stage: Build
  image: docker:19.03.1
  services:
    - docker:19.03.1-dind
  variables:
DOCKER_TLS_CERTDIR: "/certs"
  script:
    - docker info
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY
    - docker pull $CI_REGISTRY_IMAGE:latest || true
    - docker build . --cache-from $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:latest

deploy to k8s:
  image:
name: gitlab.local:5050/development-group/dev-kubectl:latest
entrypoint: ["/bin/sh", "-c"]
  only:
    - master
  stage: Deploy
  script:
    - kubectl version
    - cd manifests/
    - kubectl apply -f deployment.yaml
```

This example consists of 3 stages called Test, Build and Deploy.  

* The first stage called test consists of 2 actions, `flake8 linter` and `http test`.  Gitlab will add our code into a docker container based on the `python:3.7-alpine3.10` image and run the associated commands in the script section.  

* If no errors are thrown during the actions in the test stage the build stage will start.  In this case the action is going to use an image that runs docker so we can make a new container from our code and then upload that image to our private gitlab repository.  The creds for that repository are fed into the action via variables.  

* If the build stage completes without any errors the deploy stage will start.  In this case he action will use container from our private docker repository and run some scripts to deploy our code to kubernetes.  This action contains an only clause, in which case it will only run if the code that was committed is in the master branch.  


