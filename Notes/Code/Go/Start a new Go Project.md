# Start a new Go Project

## Create new folder
```bash
mkdir mygoapp
cd mygoapp
```

## Create go main app
```bash
touch main.go
```

Edit the file and add your initial go Code
```Go
package main

import "fmt"

func main() {
    fmt.Println("hello world")
}
```

## Initialize Go Module
```bash
go mod init <app_name>
```

## Install any external required packages
```bash
go get github.com/joho/godotenv
```

## Upgrade package versions later on
```bash
go get -u all
```