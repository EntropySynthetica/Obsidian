## Install sysbench
```
sudo apt install sysbench 
```


## Test CPU
Single Thread Test
```
sudo sysbench --test=cpu --cpu-max-prime=20000 run 
```


Multi Thread Test
```
sudo sysbench --test=cpu --cpu-max-prime=20000 --threads=32 run
```


## Storage Test
```
sudo sysbench --test=fileio --file-total-size=16G prepare 
sudo sysbench --test=fileio --file-total-size=16G --file-test-mode=rndrw run 
sudo sysbench --test=fileio --file-total-size=16G cleanup 

```

## Ram Test
```
sudo sysbench --test=memory run
```
