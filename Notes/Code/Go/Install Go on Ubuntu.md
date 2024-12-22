---
tags: [Code]
title: Install Go on Ubuntu
created: '2020-05-15T01:22:24.001Z'
modified: '2020-12-24T01:16:44.671Z'
---

# Install Go on Ubuntu

Find the latest stable version at https://golang.org/dl/

Switch to /tmp
`cd /tmp`

Download it with
`curl -O https://dl.google.com/go/go1.23.1.linux-amd64.tar.gz`

Extract the File

`tar -xvf go1.23.1.linux-amd64.tar.gz`

Adjust the permissions and move the go directory to /usr/local:

`sudo chown -R root:root ./go`
`sudo mv go /usr/local`

edit `~/.bashrc` and add this to the end. 


```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

Restart your shell

Verify with `go version`

```
go version
// go version go1.19 linux/amd64
```

Verify go ENV with `go env`

