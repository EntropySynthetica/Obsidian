---
tags: [Kubernetes]
title: Install Helm
created: '2020-02-18T00:39:50.164Z'
modified: '2020-06-10T20:30:05.506Z'
---

# Install Helm

## Pre-req 

Have kubectl installed and connected to the cluster with the correct kubeconfig yaml file. 

## Install helm CLI on your machine
Every release of Helm provides binary releases for a variety of OSes. These binary versions can be manually downloaded and installed.

* Download your https://github.com/helm/helm/releases

* Unpack it `tar -zxvf helm-v3.2.3-linux-amd64.tar.gz`

* Find the helm binary in the unpacked directory, and move it to its desired destination `mv linux-amd64/helm /usr/local/bin/helm`

## Verify install and check version

`helm version`

## Add the repository for official charts

`helm repo add stable https://kubernetes-charts.storage.googleapis.com`

Update the repo
`helm repo update`

## Install a chart 

`helm install <deployment_name> <chart_name> --namespace <namespace> --set <property_value_to_change>`

## Remove a chart

`helm uninstall <deployment_name> --namespace <namespace>`

## List what is installed

`helm list --namespace <namespace>`

## Genral usage notes
https://helm.sh/docs/intro/using_helm/

