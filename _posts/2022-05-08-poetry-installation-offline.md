---
layout: post
title: "Poetry installation for offline machine"
date: 2022-05-08 17:00:00 +0300
tags: poetry
---

What if you are using Poetry and due to some restrictions should install it on machine without internet?

After searching for the existing solutions, I only found GitHub issues like [that](https://github.com/python-poetry/poetry/issues/1783) with the same questions but without exact answers, so I decided to research it myself.

Finally I found pipeline for installing poetry offline with [new installer](https://python-poetry.org/docs/master/#installation) for the current Poetry implementation.

Let's assume for the next steps that:
- For the installation we choose poetry version 1.1.13
- Ubuntu 20.04 is used
- Python is installed
- Source and destination directories are located in `/tmp/poetry_dist`
<br/><br/>

#### Machine with internet
**Create and navigate to the directory where distribution will be prepared**
- `cd /tmp && mkdir poetry_dist && cd poetry_dist`

**Choose poetry version required for installation**  
- You can easily check which poetry versions are available for pip >= 21.2:   
`pip index versions poetry`  

  For older versions you can find corresponding method [here](https://stackoverflow.com/a/26664162).  

**Prepare required packages**  
- Save current installation code to the local file:   
`curl -o poetry_install.py -sSL https://install.python-poetry.org`

- Modify code to use local folder with wheels to install (Poetry and its dependencies)  
`sed -i 's/"install", specification/"install", "--no-index", "--find-links=.", specification/g' poetry_install.py`

- Select version and download all required packages   
`env VERSION=1.1.13 bash -c 'pip download poetry==$VERSION'`

**Archive required files**  
- `tar -czvf poetry_dist.tar.gz poetry_install.py *.whl --remove-files`

**Copy archive to the machine without internet**
- Destination folder: `/tmp/poetry_dist`
<br/><br/>

#### Machine without internet  

**Navigate to the directory where distribution where archive is located**  
- `cd /tmp/poetry_dist`

**Extract archive**
- `tar -xzvf poetry_dist.tar.gz`

**Install poetry**
- Uninstall existing version of poetry (if any):  
`python poetry_install.py --uninstall`

- Install poetry from local file:  
`VERSION=1.1.13; python poetry_install.py --path $(ls poetry-$VERSION*.whl)`

- Delete intermediate files  
`rm poetry_install.py *.whl`

**Check that installation was successful**  
- `poetry --version`
<br/><br/>

#### Conclusion
Probably it will be great to be able to pass pip arguments to [install_poetry()](https://github.com/python-poetry/poetry/blob/edabfce155c5c5445371115d5a83b792b0dc0de2/install-poetry.py#L636) function to be able to reproduce these steps without installation code modification.
<br/><br/>

#### Shell scripts
Also I created shell scripts in [repository](https://github.com/labdmitriy/poetry-offline) to automate these steps:
- `./poetry_dist_prepare.sh`  
  Prepare distribution for specific Poetry version in `/tmp/poetry_dist` folder.  

  Usage example (on machine with internet):  
  `poetry_dist_prepare.sh 1.1.13`

- `./poetry_dist_install.sh`  
  Extract distribution archive located in `/tmp/poetry_dist` folder and install Poetry offline. 

  Usage example (on machine without internet):  
  `poetry_dist_install.sh 1.1.13`
