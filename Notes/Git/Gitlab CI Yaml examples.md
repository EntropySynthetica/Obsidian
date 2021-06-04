---
tags: [Git]
title: Gitlab CI Yaml examples
created: '2020-01-30T20:16:15.563Z'
modified: '2020-09-01T18:11:37.252Z'
---

# Gitlab CI Yaml examples
Created Monday 27 January 2020



```
image: docker:git
services:
  - docker:dind
variables:
  DOCKER_DRIVER: overlay

before_script:
  - docker login -u gitlab-ci-token -p "$CI_BUILD_TOKEN" "$CI_REGISTRY"

development:
  stage: deploy
  script:
    - docker build -t "$CI_REGISTRY_IMAGE:latest" .
    - docker push "$CI_REGISTRY_IMAGE:latest"
  only:
    - master

testing:
  stage: deploy
  script:
    - docker build -t "$CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}" .
    - docker push "$CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}"
  only:
    - branches
  except:
    - master

stable:
  stage: deploy
  script:
    - docker build -t "$CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}" .
    - docker push "$CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}"
  only:
    - tags
```

---

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
          -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
          -t $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
          -t $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
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
    - kubectl apply -f service.yaml
    - kubectl apply -f ingress.yaml
    - kubectl patch -f deployment.yaml -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"ci-last-updated\":\"$(date +'%s')\"}}}}}"

```

---


```
image:
  name: golang:1.12.5-stretch
  entrypoint: ["/bin/sh", "-c"]

stages:
  - test
  - build
  - release
  - review
  - deploy

test:
  stage: test
  script:
- make test

test2:
  stage: test
  script:
- sleep 3
- echo "We did it! Something else runs in parallel!"

compile:
  stage: build
  script:
# Add here all the dependencies, or use glide/govendor/...
# to get them automatically.
- make build
  artifacts:
paths:
  - app

# Example job to upload the built release to a S3 server with mc
# For this you need to set `S3_ACCESS_KEY` and `S3_SECRET_KEY` in your GitLab project CI's secret variables
#release_upload:
#  stage: release
#  image:
#    name: minio/mc
#    entrypoint: ["/bin/sh", "-c"]
#  script:
#    - echo "=> We already have artifact sotrage in GitLab! This is for demonstational purposes only."
#    - mc config host add edenmalmoe <https://s3.edenmal.net> ${ACCESS_KEY} ${SECRET_KEY} S3v4
#    - mc mb -p edenmalmoe/build-release-${CI_PROJECT_NAME}/
#    - mc cp app edenmalmoe/build-release-${CI_PROJECT_NAME}/

image_build:
  stage: release
  image:
name: docker:latest
entrypoint: ["/bin/sh", "-c"]
  variables:
DOCKER_HOST: <tcp://localhost:2375>
  services:
- docker:dind
  script:
- docker info
- docker login -u "${CI_REGISTRY_USER}" -p "${CI_REGISTRY_PASSWORD}" "${CI_REGISTRY}"
- docker build -t "${CI_REGISTRY_IMAGE}:latest" .
- docker tag "${CI_REGISTRY_IMAGE}:latest" "${CI_REGISTRY_IMAGE}:${CI_COMMIT_REF_NAME}"
- test ! -z "${CI_COMMIT_TAG}" && docker push "${CI_REGISTRY_IMAGE}:latest"
- docker push "${CI_REGISTRY_IMAGE}:${CI_COMMIT_REF_NAME}"

deploy_review:
  image:
name: lachlanevenson/k8s-kubectl:latest
entrypoint: ["/bin/sh", "-c"]
  stage: review
  only:
- branches
  except:
- tags
  environment:
name: review/$CI_BUILD_REF_NAME
url: <https://$CI_ENVIRONMENT_SLUG-presentation-gitlab-k8s.edenmal.net>
on_stop: stop_review
  script:
- kubectl version
- cd manifests/
- sed -i "s~__CI_REGISTRY_IMAGE__~${CI_REGISTRY_IMAGE}~" deployment.yaml
- sed -i "s/__CI_ENVIRONMENT_SLUG__/${CI_ENVIRONMENT_SLUG}/" deployment.yaml ingress.yaml service.yaml
- sed -i "s/__VERSION__/${CI_COMMIT_REF_NAME}/" deployment.yaml ingress.yaml service.yaml
- |
  if kubectl apply -f deployment.yaml | grep -q unchanged; then
  echo "=> Patching deployment to force image update."
  kubectl patch -f deployment.yaml -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"ci-last-updated\":\"$(date +'%s')\"}}}}}"
  else
  echo "=> Deployment apply has changed the object, no need to force image update."
  fi
- kubectl apply -f service.yaml || true
- kubectl apply -f ingress.yaml
- kubectl rollout status -f deployment.yaml
- kubectl get deploy,svc,ing,pod -l app="$(echo ${CI_PROJECT_NAME} | tr "." "-")",ref="${CI_ENVIRONMENT_SLUG}"

stop_review:
  image:
name: lachlanevenson/k8s-kubectl:latest
entrypoint: ["/bin/sh", "-c"]
  stage: review
  variables:
GIT_STRATEGY: none
  when: manual
  only:
- branches
  except:
- master
- tags
  environment:
name: review/$CI_BUILD_REF_NAME
action: stop
  script:
- kubectl version
- kubectl delete ing -l ref=${CI_ENVIRONMENT_SLUG}
- kubectl delete all -l ref=${CI_ENVIRONMENT_SLUG}

deploy_live:
  image:
name: lachlanevenson/k8s-kubectl:latest
entrypoint: ["/bin/sh", "-c"]
  stage: deploy
  environment:
name: live
url: <https://live-presentation-gitlab-k8s.edenmal.net>
  only:
- tags
  when: manual
  script:
- kubectl version
- cd manifests/
- sed -i "s~__CI_REGISTRY_IMAGE__~${CI_REGISTRY_IMAGE}~" deployment.yaml
- sed -i "s/__CI_ENVIRONMENT_SLUG__/${CI_ENVIRONMENT_SLUG}/" deployment.yaml ingress.yaml service.yaml
- sed -i "s/__VERSION__/${CI_COMMIT_REF_NAME}/" deployment.yaml ingress.yaml service.yaml
- kubectl apply -f deployment.yaml
- kubectl apply -f service.yaml
- kubectl apply -f ingress.yaml
- kubectl rollout status -f deployment.yaml
- kubectl get deploy,svc,ing,pod -l app="$(echo ${CI_PROJECT_NAME} | tr "." "-")",ref="${CI_ENVIRONMENT_SLUG}"
```
