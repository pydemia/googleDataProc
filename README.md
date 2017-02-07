# googleDataProc
Google Cloud Platform for Hadoop Cluster



### 
```sh
sudo gcloud init
```

In case if you have multiple python versions, you should use ```python2.7``` for ```gcloud```:
```sh
vi ~/.bashrc

 # Google Cloud Platform PYTHONPATH
132 export CLOUDSDK_PYTHON=/usr/lib/python2.7



source ~/.bashrc
```

### SSH

```sh
sudo gcloud compute ssh pydemia@hdcluster-m-0	--zone 
```

```sh
sudo gcloud compute instances create temp-machine --scopes compute-rw
sudo gcloud compute ssh temp-machine
```

On Instance:
```sh
sudo useradd -m tempuser
sudo su - tempuser
sudo gcloud compute ssh example-instance
```


```sh
sudo gcloud compute ssh temp-machine

```

## Connection

```sh
sudo gcloud compute ssh --zone=asia-northeast1-a --ssh-flag="-D 1080" --ssh-flag="-N" --ssh-flag="-n"  hdcluster-m-0
```


```sh
sudo gcloud compute instances describe hdcluster-m-0 --format yaml | grep natIP
    natIP: 104.198.94.101

nc 104.198.94.101 22
SSH-2.0-OpenSSH_6.7p1 Debian-5+deb8u3



```

## Import 

```sh
mysql -h104.155.198.17 -upydemia -p -t < employees.sql
```

```sh
gcloud beta sql connect pydemiamysql --user=pydemia
```
