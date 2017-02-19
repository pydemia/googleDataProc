# googleDataProc
Google Cloud Platform for Hadoop Cluster



## Install `google-cloud-sdk`

### Setup `python2.7.x` Environment
In case if you have multiple python versions, you should use ```python2.7``` for ```gcloud```:

```sh
vi ~/.bashrc
```

```sh
# Google Cloud Platform PYTHONPATH
export CLOUDSDK_PYTHON=~/anaconda2/bin/python2
```
```sh
source ~/.bashrc
```

### Download and Install `google-cloud-sdk`

```sh
cd ~/apps
curl https://sdk.cloud.google.com
sudo chmod -R 777 google-cloud-sdk
bash google-cloud-sdk/install.sh

```

```sh
Welcome to the Google Cloud SDK!

To help improve the quality of this product, we collect anonymized usage data
and anonymized stacktraces when crashes are encountered; additional information
is available at <https://cloud.google.com/sdk/usage-statistics>. You may choose
to opt out of this collection now (by choosing 'N' at the below prompt), or at
any time in the future by running the following command:

    gcloud config set disable_usage_reporting true

Do you want to help improve the Google Cloud SDK (Y/n)?  y


This will install all the core command line tools necessary for working with
the Google Cloud Platform.


All components are up to date.

Modify profile to update your $PATH and enable shell command 
completion? (Y/n)?  y

The Google Cloud SDK installer will now prompt you to update an rc 
file to bring the Google Cloud CLIs into your environment.

Enter a path to an rc file to update, or leave blank to use 
[/home/dawkiny/.bashrc]:  
Backing up [/home/dawkiny/.bashrc] to [/home/dawkiny/.bashrc.backup].
[/home/dawkiny/.bashrc] has been updated.

==> Start a new shell for the changes to take effect.


For more information on how to get started, please visit:
  https://cloud.google.com/sdk/docs/quickstarts

```

In case If you have a problem with the following:
```sh
IOError: [Errno 13] Permission denied: '/home/dawkiny/.bashrc.backup'
```

Then try this:
```sh
sudo chown dawkiny:dawkiny ~/.bashrc.backup
```


### Initiate `gcloud`

```sh
gcloud init
```

You would meet some pages on web browsers for setting.

```sh
Welcome! This command will take you through the configuration of gcloud.

Your current configuration has been set to: [default]

You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.                                             
Reachability Check passed.
Network diagnostic (1/1 checks) passed.

You must log in to continue. Would you like to log in (Y/n)?  y

Your browser has been opened to visit:

    https://accounts.google.com/o/oauth2/auth?redirect_uri=http%3A%2F%2Flocalhost%3A8085%2F&prompt=select_account&response_type=code&client_id=32555940559.apps.googleusercontent.com&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fuserinfo.email+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fappengine.admin+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcompute+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Faccounts.reauth&access_type=offline


Created new window in existing browser session.
You are logged in as: [pydemia@gmail.com].

Your current project has been set to: [pydemiahdc].

Do you want to configure Google Compute Engine 
(https://cloud.google.com/compute) settings (Y/n)?  y

Which Google Compute Engine zone would you like to use as project 
default?
If you do not specify a zone via a command line flag while working 
with Compute Engine resources, the default is assumed.
 [1] asia-east1-a
 [2] asia-east1-c
 [3] asia-east1-b
 [4] asia-northeast1-a
 [5] asia-northeast1-b
 [6] asia-northeast1-c
 [7] europe-west1-d
 [8] europe-west1-b
 [9] europe-west1-c
 [10] us-central1-b
 [11] us-central1-f
 [12] us-central1-a
 [13] us-central1-c
 [14] us-east1-c
 [15] us-east1-d
 [16] us-east1-b
 [17] us-west1-a
 [18] us-west1-b
 [19] Do not set default zone
Please enter numeric choice or text value (must exactly match list 
item):  4

Your project default Compute Engine zone has been set to [asia-northeast1-a].
You can change it by running [gcloud config set compute/zone NAME].

Your project default Compute Engine region has been set to [asia-northeast1].
You can change it by running [gcloud config set compute/region NAME].

Your Google Cloud SDK is configured and ready to use!

* Commands that require authentication will use pydemia@gmail.com by default
* Commands will reference project `pydemiahdc` by default
* Compute Engine commands will use region `asia-northeast1` by default
* Compute Engine commands will use zone `asia-northeast1-a` by default

Run `gcloud help config` to learn how to change individual settings

This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.

Some things to try next:

* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic -h` to learn about advanced features of the SDK like arg files and output formatting

```

### Create a Cluster (with Jupyter Notebook)

#### Create a Bucket(Storage)
```sh

```

#### Create `jupyter/jupyter.sh` in the bucket.
```sh
#!/usr/bin/env bash
set -e

ROLE=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/dataproc-role)
INIT_ACTIONS_REPO=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/INIT_ACTIONS_REPO || true)
INIT_ACTIONS_REPO="${INIT_ACTIONS_REPO:-https://github.com/GoogleCloudPlatform/dataproc-initialization-actions.git}"
INIT_ACTIONS_BRANCH=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/INIT_ACTIONS_BRANCH || true)
INIT_ACTIONS_BRANCH="${INIT_ACTIONS_BRANCH:-master}"
DATAPROC_BUCKET=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/dataproc-bucket)

JUPYTER_CONDA_PACKAGES=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/JUPYTER_CONDA_PACKAGES || true)

echo "Cloning fresh dataproc-initialization-actions from repo $INIT_ACTIONS_REPO and branch $INIT_ACTIONS_BRANCH..."
git clone -b "$INIT_ACTIONS_BRANCH" --single-branch $INIT_ACTIONS_REPO
./dataproc-initialization-actions/conda/bootstrap-conda.sh

source /etc/profile.d/conda_config.sh

if [ -n "${JUPYTER_CONDA_PACKAGES}" ]; then
  echo "Installing custom conda packages '$(echo ${JUPYTER_CONDA_PACKAGES} | tr ':' ' ')'"
  conda install $(echo ${JUPYTER_CONDA_PACKAGES} | tr ':' ' ')
fi

if [[ "${ROLE}" == 'Master' ]]; then
    conda install jupyter
    if gsutil -q stat "gs://$DATAPROC_BUCKET/notebooks/**"; then
        echo "Pulling notebooks directory to cluster master node..."
        gsutil -m cp -r gs://$DATAPROC_BUCKET/notebooks /root/
    fi
    ./dataproc-initialization-actions/jupyter/internal/setup-jupyter-kernel.sh
    ./dataproc-initialization-actions/jupyter/internal/launch-jupyter-kernel.sh
fi
echo "Completed installing Jupyter!"

if [[ ! -v $INSTALL_JUPYTER_EXT ]]
    then
    INSTALL_JUPYTER_EXT=false
fi
if [[ "$INSTALL_JUPYTER_EXT" = true ]]
then
    echo "Installing Jupyter Notebook extensions..."
    ./dataproc-initialization-actions/jupyter/internal/bootstrap-jupyter-ext.sh
    echo "Jupyter Notebook extensions installed!"
fi

```sh

#### Create a cluster using the bucket.

```sh
gs://dataproc-initialization-actions/jupyter/jupyter.sh
gs://dataproc-initialization-actions/zeppelin/zeppelin.sh
gs://dataproc-initialization-actions/hue/hue.sh
gs://dataproc-initialization-actions/tez/tez.sh
gs://dataproc-initialization-actions/zookeeper/zookeeper.sh
gs://dataproc-initialization-actions/oozie/oozie.sh
gs://dataproc-initialization-actions/kafka/kafka.sh
gs://dataproc-initialization-actions/flink/flink.sh
gs://dataproc-initialization-actions/presto/presto.sh
```

```sh
gs://hdc_init/init/jupyter/jupyter.sh
gs://hdc_init/init/zeppelin/zeppelin.sh

gs://hdc_init/init/zookeeper/zookeeper.sh
gs://hdc_init/init/oozie/oozie.sh
gs://hdc_init/init/kafka/kafka.sh
gs://hdc_init/init/flink/flink.sh
gs://hdc_init/init/presto/presto.sh
gs://hdc_init/init/hue/hue.sh


gs://hdc_init/init/tez/tez.sh
```

#### Set SSH Key
```sh
gcloud compute ssh --zone=asia-northeast1-a --ssh-flag="-D" --ssh-flag="10000" --ssh-flag="-N" "hdc-m"
gcloud compute ssh --zone=asia-northeast1-a --ssh-flag="-D" --ssh-flag="10000" --ssh-flag="-N" --ssh-flag="-n" "hdc-m"
```

```sh
WARNING: The private SSH key file for gcloud is not readable.
WARNING: Your SSH key files are broken.
private key (BROKEN) [/home/dawkiny/.ssh/google_compute_engine]
public key  (OK)     [/home/dawkiny/.ssh/google_compute_engine.pub]
We are going to overwrite all above files.
Do you want to continue (y/N)?  y

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/dawkiny/.ssh/google_compute_engine.
Your public key has been saved in /home/dawkiny/.ssh/google_compute_engine.pub.
The key fingerprint is:
SHA256:VkVtdgQnWRcsc1pgDI99T6jF3yyob2/PXVTj2rQqDl8 dawkiny@terriand
The key's randomart image is:
+---[RSA 2048]----+
|           o=+=**|
|           .===Bo|
|          .. =X+o|
|         .   =o+=|
|        S   o .o*|
|       .   .  +o.|
|         ..  E o.|
|          ooo o.o|
|          .+o+..+|
+----[SHA256]-----+
Updating project ssh metadata.../Updated [https://www.googleapis.com/compute/v1/projects/pydemiahdc].
Updating project ssh metadata...done.                                           
Failed to add the host to the list of known hosts (/home/dawkiny/.ssh/google_compute_known_hosts).
Failed to add the host to the list of known hosts (/home/dawkiny/.ssh/google_compute_known_hosts).
bind: Address already in use
```

Run this:
```sh
<browser executable path> \
    "http://<cluster-name>-m:8123" \
    --proxy-server="socks5://localhost:10000" \
    --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" \
    --user-data-dir=/tmp/
```

For example:

```sh
/usr/bin/google-chrome-stable %U \
    "http://104.198.86.252:8123" \
    --proxy-server="socks5://localhost:10000" \
    --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" \
    --user-data-dir=/tmp/


/usr/bin/google-chrome-stable %U \
    "http://hdc-m:8123" \
    --proxy-server="socks5://localhost:10000" \
    --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" \
    --user-data-dir=/tmp/
```

In case if you fail, check out the proxy setting on chrome and re-try it.

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
