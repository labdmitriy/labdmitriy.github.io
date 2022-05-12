---
title: "Poetry installation for offline machine"
date: 2022-05-08 17:00:00 +0300
toc: true
categories:
- Blog
tags: 
- Poetry
---

## Motivation
What if you are using Poetry and due to some restrictions should install it on machine without internet?

After searching for the existing solutions, I only found GitHub issues like [that](https://github.com/python-poetry/poetry/issues/1783) with the same questions but without exact answers, so I decided to research it myself.

Finally I found pipeline for installing poetry offline with [new installer](https://python-poetry.org/docs/master/#installation) for the current Poetry implementation.

## Assumptions
Let's assume for the next steps that:
- For the installation we choose poetry version 1.1.13
- Ubuntu 20.04 is used
- Python is installed
- Source and destination directories are located in `/tmp/poetry_dist`

## Machine with internet
Create and navigate to the directory where distribution will be prepared:
```shell
cd /tmp && mkdir poetry_dist && cd poetry_dist
```

Choose poetry version required for installation.  
You can easily check which poetry versions are available for pip >= 21.2 (for older versions you can find corresponding method [here](https://stackoverflow.com/a/26664162)):
```shell
pip index versions poetry
```

Save current installation code to the local file:
```shell
curl -o poetry_install.py -sSL https://install.python-poetry.org
```

Modify code to use local folder with wheels to install (Poetry and its dependencies):
```shell
sed -i 's/"install", specification/"install", "--no-index", "--find-links=.", specification/g' poetry_install.py
```

Select version and download all required packages:
```shell
env VERSION=1.1.13 bash -c 'pip download poetry==$VERSION'
```

Archive required files:
```shell
tar -czvf poetry_dist.tar.gz poetry_install.py *.whl --remove-files
```

Copy archive to the machine without internet to the destination folder:
```shell
/tmp/poetry_dist
```

## Machine without internet  
Navigate to the directory where distribution archive is located:
```shell
cd /tmp/poetry_dist
```

Extract archive:
```shell
tar -xzvf poetry_dist.tar.gz
```

Uninstall existing version of poetry (if any):
```shell
python poetry_install.py --uninstall
```

Install poetry from local file:  
```shell
env VERSION=1.1.13 bash -c 'python poetry_install.py --path $(ls poetry-$VERSION*.whl)'
```

Delete intermediate files  
```shell
rm poetry_install.py *.whl
```

Check that installation was successful
```shell
poetry --version
```

## Conclusion
Probably it will be great to be able to pass pip arguments to [install_poetry()](https://github.com/python-poetry/poetry/blob/edabfce155c5c5445371115d5a83b792b0dc0de2/install-poetry.py#L636) function to reproduce these steps without installation code modification.
<br/>

## Shell scripts
Also I created shell scripts in [repository](https://github.com/labdmitriy/poetry-offline) to automate these steps:

**poetry_dist_prepare.sh**  
Prepare distribution for specific Poetry version in `/tmp/poetry_dist` folder.  
Usage example (on machine with internet):  
```shell
./poetry_dist_prepare.sh 1.1.13
```
<br/>
**poetry_dist_install.sh**  
Extract distribution archive located in `/tmp/poetry_dist` folder and install Poetry offline. 
Usage example (on machine without internet):
```shell
./poetry_dist_install.sh 1.1.13
```
