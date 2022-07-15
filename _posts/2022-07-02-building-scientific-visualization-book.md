---
title: "Building Scientific Visualization book"
excerpt: How to build the latest version of the book from scratch.
date: 2022-07-02 00:00:00 +0300
last_modified_at: 2022-07-15 09:00:00 +0300
toc: true
categories:
- Blog
tags:
- Open Source
- Visualization
- Book
- Python
---

## Motivation
A few months ago I found the recommendation of the book about visualization using Python - [Scientific Visualization: Python + Matplotlib](https://github.com/rougier/scientific-visualization-book).  

Skimming through the content I was very interested in reading and studying it because of the following advantages of this book:
- The author ([Nicolas Rougier](https://github.com/rougier)) has a lot of experience including visualization and open source development - just open his GitHub home page and you will understand how much :smiley:
- There are exercices which will allow to practice and understand the theory better
- The book and the supplemental code are public in the [repository](https://github.com/rougier/scientific-visualization-book) and
this repository is also can be used to build the latest version of the book from scratch, including fixed typos and errors

But the last statement has a small problem - there were no instructions how to build the book and to prepare environment to reproduce the code, and the latest public build of the book was in November, 2021.  

There were quite a lot of closed issues and it would be great to build a new version to account all these fixes. So I decided to explore and resolve it if possible.

## Path to the solution
First of all I tried to solve it myself but if was unsuccessful because I didn't have any experience in compilation of LaTeX projects so I decided to ask the author.  

I created the [issue](https://github.com/rougier/scientific-visualization-book/issues/51) with the description of my failed attempts, and many thanks to the author that after a while he replied and gave the key hint to use the Python package of specific version.  

After that I've made the first successful build pretty quickly, but this process has several drawbacks:
- It was heavy (> 8 GB of disk size) and slow (~ 1 hour) to install all the components and configure it correctly
- At the same moment I tried to configure the environment to execute the code while studying, so there were 2 parallel processes with unclear steps
- The book was built on Ubuntu 20.04 for my Python projects so I didn't know if these steps would be sufficient to reproduce it from scratch with clean OS
- It was heavily dependent on root user for TeX Live installation
- Finally it was not stable - the next day, when I tried to repeat the steps, I found strange incompatibility errors between some packages, and I didn't know how to fix it  

And I realized that I needed to take a step back and find a more quick, lightweight and stable solution that other users could use.


## Solution
### OS
I found clean version of Ubuntu 20.04 image for the virtual machine to be able to speed up the iterations of my experiments.


### Install TeX Live
The author uses [TeX Live](https://tug.org/texlive/) and by default if you install it using command line, [full scheme](https://tug.org/svn/texlive/trunk/Master/tlpkg/tlpsrc/scheme-full.tlpsrc?view=markup) will be used.  
About 7.6 GB will be used only for the full installation of TeX Live, so I explored different schemes and found the solution - to use [basic scheme](https://tug.org/svn/texlive/trunk/Master/tlpkg/tlpsrc/scheme-basic.tlpsrc?view=markup) and install additional packages by following building errors.  
Also I installed TeX Live to user's home directory which will which greatly simplified the work with TeX Live Manager (`tlmgr`). 

```bash
# Download and unpack Tex Live installer
mkdir -p $download_dir
cd $download_dir
wget https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
tar -xzvf install-tl-unx.tar.gz
cd install-tl-*/

# Install portable version of TeX Live with basic scheme and fixed CTAN repository
export TEXLIVE_INSTALL_PREFIX=$HOME/texlive
export TEXLIVE_INSTALL_TEXDIR=$HOME/texlive
perl install-tl -scheme scheme-basic -portable -no-interaction -repository $ctan_url/systems/texlive/tlnet/ 
echo -e '\n#texlive\nexport PATH="$PATH:$HOME/texlive/bin/x86_64-linux"\n' >> ~/.bashrc
source ~/.bashrc

# Install additional TeX Live packages
tlmgr install -repository $ctan_url/systems/texlive/tlnet/ latexmk ean13isbn ean ocr-b helvetic enumitem units emptypage biblatex quoting titlesec tocloft mdframed zref needspace biber xetex xcolor pdfpages hologo float pgf parskip fontspec microtype listings caption booktabs pdflscape
```
Where `$download_dir` is the folder to download TeX Live installer, and `$ctan_url` is URL to the one of [CTAN mirrors](https://www.ctan.org/mirrors) which are used to install TeX Live packages.   

Fixing the mirror was very important because this strange error with packages incompatibility which I mentioned above was for 2 packages - `biber` and `biblatex`:
> Biber error: [345] Utils.pm:409> ERROR - Error: Found biblatex control file version 3.9, expected version 3.8.

I didn't understand how to solve this problem but after a lot of experiments I found that the CTAN mirror could be different for each execution (if you don't specify it manually) and this choice is based on [internal logic](https://tex.stackexchange.com/questions/287166/how-does-the-tex-live-manager-pick-mirrors-if-none-is-manually-specified).  
So I fixed the repository that doesn't have this problem at this moment - [https://ctan.altspu.ru](https://ctan.altspu.ru).

Also I included `-portable` argument to have more system and version independent distribution:
> The -portable installer option (or V command in the text installer or corresponding GUI option) creates a completely self-contained TeX Live installation under a common root and forgoes system integration.

This step was the most time consuming but using this approach I have reduced the installation size of TeX Live from ~7.6 GB to ~450 MB.


### Update OS packages sources info
To install all the required latest versions of the OS packages, we need to update information about it:
```bash
sudo apt-get update
```

### Create Python virtual environment
In Ubuntu 20.04 the Python version included in the base system is 3.8, and it will be sufficient for our task. Now we just need to create virtual environment for the isolation of our project:
```bash
# Install pip and venv for system Python
sudo apt-get -y install python3-pip python3-venv

# Create and activate virtural environment
mkdir -p $venv_dir
python3 -m venv $venv_dir/scientific-visualization-book
source $venv_dir/bin/activate

# Install required Python packages
python3 -m pip install --upgrade pip setuptools wheel
pip install docutils==0.17
```
Where `$venv_dir` is the folder where you want to create this new virtual environment.

Installing `docutils` package of version 0.17 was the key hint from the author for this step (thanks to the author again!).   
By the way, installing only this Python package (and its dependencies) was enough for further successful book compilation.


### Clone the repository
Let's download the repository with the book from GitHub:
```bash
# Install git
sudo apt-get -y install git

# Create project directory and clone the repository
mkdir -p $project_dir
cd $project_dir
git clone https://github.com/rougier/scientific-visualization-book
cd scientific-visualization-book
```
Where `$project_dir` is the folder where you want to clone the repository.  


### Build the book
When everything is installed and configured and we are in repository's root folder, we can start the process of the book building:
```bash
make clean && make all
```
After this step, the new version of the book will be created in the `pdf` subfolder.


## Shell script
To make life easier, I prepared [Bash script](https://github.com/labdmitriy/sci-viz-book/blob/master/scripts/build_book.sh) which will reproduce all these steps.  
This script has 4 arguments that we'll already discussed above:
```bash
. ./build-sci-viz-book.sh <ctan_url> <download_dir> <venv_dir> <project_dir>
```

For example, my configuration is the following:
```bash
. ./build-sci-viz-book.sh https://ctan.altspu.ru ~/Downloads ~/venvs ~/education/sci-viz
```

The first "." symbol is necessary to properly [propagate changes of .bashrc in the script](https://askubuntu.com/a/1041348) so all the changes will be executed in the current shell process.  

Also I added the ability to measure the time of the script execution.


### Further builds
After everything is configured and executed for the first time, the next builds will be much easier:
```bash
cd <project_dir>
source <venv_dir>/scientific-visualization-book/bin/activate
make clean && make all
```

### PDF compression
Also I found that there is another option in `Makefile` which allows to slightly reduce the size of the file:
```bash
make pdf-compressed
```

It will take some time and as a result the compressed file is about 22% less than the original one (~73MB vs ~94MB).
I haven't checked the compressed version of the file thoroughly but maybe it will be useful for someone.


## Results
The final solution uses only ~450 MB of TeX Live installation, Python virtual environment with only one explicit package installed, and it takes ~15 minutes to make the successful build from scratch.


## The next steps
Now I am ready to start studying this book and probably will describe my progress and environment preparation for the code execution in the following posts of this blog.


## Feedback
I would be grateful for any feedback on improving this process or if you find any errors.
