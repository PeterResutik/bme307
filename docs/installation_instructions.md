# Installation instructions


We ask all students to bring their own laptop and to install the required software before the beginning of the practicals. We will provide a troubleshooting session on Tuesday (19.9.2023), after the lecture, for those who need to resolve installation issues.

For the course, we will be using the following software:

1. [Docker](https://www.docker.com/)
* [QIIME2](https://qiime2.org/) <!--  R to visually explore the sequencing data and to conduct statistical analysis -->
* [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
 
Please follow the instructions provided according to the operating system (OS) you are using. Feel free to email us with questions if you encounter installations issues bioinformatics.bme307@gmail.com. See you soon! 

## For Windows Users:

### 1. Docker + QIIME2 

!!! warning "Is "Virtualization" enabled?"
    - Press Ctrl + Alt + Del
    - Select Task Manager
    - Click the Performance tab
    - Click CPU
    - Is "Virtualization:" enabled? 
    - If not, you can follow the instructions on how to enable virtualization in BIOS [here](https://2nwiki.2n.cz/pages/viewpage.action?pageId=75202968). 
    
    The instructions may vary depending on the laptop you are using. The link includes instructions for Acer, Assus, Dell, HP, Lenovo, Sony, and Toshiba.


 - Install Docker Desktop on Windows from [here](https://docs.docker.com/desktop/install/windows-install/)
 - Run the .exe file. 

!!! warning "If you see the following error message"
    **“Docker Desktop requires Windows 10 Pro/Enterprise (15063+) or Windows 10 Home (19018+).”**
    
    - Update Windows by clicking on the "Update now" button [here](https://www.microsoft.com/en-us/software-download/windows10) 
    - Try installing docker again
    - Run the "D.exe file. 

- When prompted, ensure the Enable WSL 2 Features option is selected on the Configuration page

!!! warning "WSL2 error"
    - If you encounter issues with WSL2, install "Linux kernel update package" from [here](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

-   Start docker 
> **_NOTE:_** search for Docker Desktip in the start menu. 

-   When the whale icon in the status bar stays steady, Docker Desktop is up-and running, and is accessible from the Windows PowerShell
-   Open Windows PowerShell and run the following command to ensure that docker is working correctly

```bash
docker run hello-world
```

Once the docker is installed and running you can run qiime2 and fastqc via the following commands: 

```bash
docker run --rm -v ${pwd}:/data/ -w /data/ -it quay.io/qiime2/core:2023.5
```


## For Mac Users:

### 1. Anaconda
 
- Install Anaconda for Mac from [here](https://repo.anaconda.com/archive/Anaconda3-2023.07-2-MacOSX-x86_64.pkg)
- Double click the .pkg file.
- Test your installation. 
- Run the following command in the Terminal 

```bash
conda list
```

### 2. QIIME2 

- Run the following commands to install QIIME2

```bash
wget https://data.qiime2.org/distro/core/qiime2-2023.5-py38-osx-conda.yml
conda env create -n qiime2-2023.5 --file qiime2-2023.5-py38-osx-conda.yml
```
- activate QIIME2 environment

```bash
conda activate qiime2-2023.5
```

<!-- ```bash
docker  pull  quay.io/qiime2/core:2023.5
``` -->

- Run the following command to test the installation

```bash
qiime --help
```

### 2. FastQC

Download and install the following [file](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.12.1.dmg). 

!!! warning "Do you have issues starting the tool due to verification?"
    - Install Java Development Kit from [here](https://www.oracle.com/java/technologies/downloads/#jdk21-mac) and try again. 

If installing Java Development Kit did not work, download the FastQC source code from [here](https://github.com/s-andrews/FastQC/archive/refs/tags/v0.12.1.zip). Unzip the file and from the terminal window, navigate to the unzipped folder on your mac. Subsequently, run the following command: 

```bash
./fastqc
```





<!-- ### 2. R and RStudio
-   [Download R for Windows here](http://cran.r-project.org/bin/windows/base/release.htm)
-   Run the .exe file that was downloaded in the step above.
-   [Download RStudio for Windows here](https://download1.rstudio.org/desktop/windows/RStudio-1.4.1717.exe)
-   Double click the file to install it
Once R and RStudio are installed, open RStudio to make sure that you do not get any error messages. 


$ docker run --rm presut/usearch usearch

  



-   If everything is running as it should, after some additional installations you will see the following output lines in the Terminal usearch v11.0.667_i86linux32, 4.0Gb RAM (13.0Gb total), 8 cores
    

(C) Copyright 2013-18 Robert C. Edgar, all rights reserved.

[https://drive5.com/usearch](https://drive5.com/usearch)

License: personal use only

## 2.  Homebrew

-   For MacOS Catalina, macOS Mojave, and MacOS Big Sur:
    

$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

-   For macOS High Sierra, Sierra, El Capitan, and earlier:
    

  

$ /usr/bin/ruby -e "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/master/install)](https://raw.githubusercontent.com/Homebrew/install/master/install)"
- For any further installations, type:
$ brew install “name of software”

Eg: Typing the following command will install R on your laptop.

$ brew install R


## 3.  R and RStudio

-   Go to [CRAN](http://cran.r-project.org/) and click on Download R for (Mac) OS X OR copy and paste the following link on your browser: [https://cran.r-project.org/](https://cran.r-project.org/)
    
-   Select the .pkg file for the version of OS X that you have and the file will be download.
    
-   Double click on the file that was downloaded and R will be installed.
    
-   [Download R-Studio for Mac here](https://download1.rstudio.org/desktop/macos/RStudio-1.4.1717.dmg)
    

OR copy and paste the following link on your browser:

[https://rstudio.com/products/rstudio/download/#download](https://rstudio.com/products/rstudio/download/#download)

-   Once it is downloaded, double click the file to install it.
    

Once R and RStudio are installed, open RStudio to make sure it works and you do not get any error messages.  

$ ## For Linux Users:

  
--> 
