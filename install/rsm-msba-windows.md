# Contents
  - [Installing the RSM-MSBA computing environment on Windows](#installing-the-rsm-msba-computing-environment-on-windows)
  - [Updating the RSM-MSBA computing environment on Windows](#updating-the-rsm-msba-computing-environment-on-windows)
  - [Connecting to postgresql](#connecting-to-postgresql)
  - [Installing R and Python packages locally](#installing-r-and-python-packages-locally)
  - [Cleanup](#cleanup)
  - [Trouble shooting](#trouble-shooting)

## Installing the RSM-MSBA computing environment on Windows

Please follow the instructions below to install the rsm-msba-spark computing environment. It has R, Rstudio, Python, Jupyter Lab, and various required packages pre-installed. The computing environment will be consistent across all students and faculty, easy to update, and also easy to remove if desired (i.e., there will *not* be dozens of pieces of software littered all over your computer).

**Step 1**: Upgrade Windows if you are currently using Windows Home Edition

Windows users **must** use Microsoft Windows 10 Professional, Education, or Enterprise (64-bit). Students will likely be able to upgrade to Microsoft Windows 10 Education (64-bit) for free through their university

**Step 2**: Install docker from the link below and make sure it is running. You will know it is running if you see the icon below in your system tray. If the containers shown in in the image are moving up and down docker hasn't finished starting up yet.

During the install process use *only* the default settings. You may be prompted to enable virtualization ("Hyper-V"). If so, click OK and your computer will restart.

![](figures/docker-icon.png)

https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe

Once the install of the docker application is done, right click on the docker icon, select "Settings", and make sure that "C" is checked as a shared drive as shown in the image below

<img src="figures/windows-shared-drives.png" width="500px">

You should also change the resources docker is allowed to use on your system. You can set this to approximately 50% of the maximum available on your system.

<img src="figures/docker-resources.png" width="500px">

Optional: If you are interested, this linked video gives a brief intro to what Docker is: https://www.youtube.com/watch?v=YFl2mCHdv24

**Step 3**: Install git bash from the link below

<a href="https://git-scm.com/download/win" target="_blank">https://git-scm.com/download/win</a>

Keep the default settings until you are asked about "Configuring extra options". Set the options as shown in the screen shot below.

<img src="figures/symlinks.png" width="500px">

There is no need to "View Release Notes" on the last screen or "Launch Git Bash" just yet.

**Step4**: Open a bash terminal **as administrator** and copy-and-paste the code below to add `rsync` functionality. Note: You may have to right-click to get a copy-and-paste menu for the terminal

> Note: To open git bash as administrator, find the app in the Start Menu, right click on the icon, and select 'Run as administrator'.

```bash
curl http://www2.futureware.at/~nickoe/msys2-mirror/msys/x86_64/rsync-3.1.2-2-x86_64.pkg.tar.xz -o rsync.pkg.tar.xz;
tar xvJf rsync.pkg.tar.xz -C /c/Program\ Files/Git/;
rm -rf rsync.pkg.tar.xz;
```

Next, copy-and-paste the code below to clone the launch scripts needed to start the docker container.

```bash
git clone https://github.com/radiant-rstats/docker.git C:/Users/$USERNAME/git/docker;
cp -p C:/Users/$USERNAME/git/docker/launch-rsm-msba-spark.sh C:/Users/$USERNAME/Desktop;
C:/Users/$USERNAME/Desktop/launch-rsm-msba-spark.sh;
```

This step will clone and start up a script that will finalize the installation of the computing environment. The first time you run this script it will download the latest version of the computing environment which can take some time. Wait for the container to download and follow any prompts. Once the download is complete you should see a menu as in the screen shot below. 

<img src="figures/rsm-msba-menu-windows.png" width="500px">

The code above also creates a copy of the file `launch-rsm-msba-spark.sh` on your Desktop that you can double-click to start the container again in the future. 

Copy-and-paste the command below to create a shortcut to the launch script to use from the command line. 

```bash
ln -s C:/Users/$USERNAME/git/docker/launch-rsm-msba-spark.sh /usr/bin/launch;
```

**Step 5**: Check that you can launch Rstudio and Jupyter

You will know that the installation was successful if you can start Rstudio and Jupyter Lab. When you press 2 (and Enter) in the terminal, Rstudio should start up in your default web browser. If you press 3 (and Enter) Jupyter Lab should start up in another tab in your web browser. For Rstudio, the username is "jovyan" and the password is "rstudio". For Jupyter the password is "jupyter"

> Important: Always use q (and Enter) to shutdown the computing environment

**Rstudio**:

<img src="figures/rsm-rstudio.png" width="500px">

**Jupyter**:

<img src="figures/rsm-jupyter.png" width="500px">

## Updating the RSM-MSBA computing environment on Windows

To update the container use the launch script and press 4 (+ enter). To update the launch script itself, press 5 (+ enter).

<img src="figures/rsm-msba-menu-windows.png" width="500px">

If for some reason you are having trouble updating either the container or the launch script open a bash terminal and copy-and-paste the code below. Note: You may have to right-click to get a copy-and-paste menu for the terminal. These commands will update the docker container, replace the old docker related scripts, and copy the latest version of the launch script to your Desktop.

```bash
docker pull vnijs/rsm-msba-spark;
rm -rf C:/Users/$USERNAME/git/docker;
git clone https://github.com/radiant-rstats/docker.git C:/Users/$USERNAME/git/docker;
cp -p C:/Users/$USERNAME/git/docker/launch-rsm-msba-spark.sh C:/Users/$USERNAME/Desktop;
```

## Connecting to postgresql

The rsm-msba-spark container comes with <a href="http://www.postgresqltutorial.com" target="_blank">postgresql</a> installed. Once the container has been started, you can access postgresql from Rstudio using the code below:

```r
## connect to database
library(DBI)
library(RPostgreSQL)
con <- dbConnect(
  dbDriver("PostgreSQL"),
  user = "jovyan",
  host = "127.0.0.1",
  port = 8765,
  dbname = "rsm-docker",
  password = "postgres"
)

## show list of tables
dbListTables(con)
```

For a more extensive example using R see: <a href="https://github.com/radiant-rstats/docker/blob/master/postgres/postgres-connect.md target="_blank">https://github.com/radiant-rstats/docker/blob/master/postgres/postgres-connect.md</a>

To access postgresql from Jupyter Lab use the code below:

```py
## connect to database
from sqlalchemy import create_engine
engine = create_engine('postgresql://jovyan:postgres@127.0.0.1:8765/rsm-docker')

## show list of tables
engine.table_names()
```

For a more extensive example using Python see: <a href="https://github.com/radiant-rstats/docker/blob/master/postgres/postgres-connect.ipynb" target="_blank">https://github.com/radiant-rstats/docker/blob/master/postgres/postgres-connect.ipynb</a>

## Installing R and Python packages locally

To install R packages that will persist after restarting the docker container, enter code like the below in Rstudio and follow any prompts:

`install.packages("fortunes", lib = Sys.getenv("R_LIBS_USER"))`

To install Python modules that will persist after restarting the docker container, enter code like the below from the terminal in Jupyter Lab:

`pip3 install --user redis`

After installing a module you will have to restart any running Python kernels to `import` the module in your code.

To remove locally installed R packages press 7 (and Enter) in the launch menu and follow the prompts. To remove locally installed Python modules press 8 (and Enter) in the launch menu.

## Cleanup

To remove any prior Rstudio sessions, and locally installed R-packages, press 6 + Enter in the launch menu. To remove locally installed Python packages press 7 + Enter in the launch menu.

You should always stop the `rsm-msba-spark` docker container using `q` (and Enter) in the launch menu. If you want a full cleanup and reset of the computational environment on your system, however, execute the following commands from a (bash) terminal to (1) remove prior R(studio) and Python settings, (2) remove all docker images, networks, and (data) volumes, and (3) 'pull' only the docker image you need (e.g., rsm-msba-spark):

```bash
rm -rf C:/Users/$USERNAME/.rstudio;
rm -rf C:/Users/$USERNAME/.rsm-msba;
docker system prune --all --volumes --force;
docker pull vnijs/rsm-msba-spark;
```

## Trouble shooting

Check if a firewall or VPN is blocking docker's access to your home directory. If this is an issue on your system, a warning should be shown when you check the "C" drive in docker settings and click "Apply". See screen shot below:

<img src="figures/reset-credentials.png" width="500px">

If there is an error related to the firewall, try turning off the firewall to check if you can now start up the container. You should not be without a virus checker or firewall however! We recommend using **Windows Defender**. If you are not sure if Windows Defender is correctly configured, please check with IT.

If you are able to select the C drive and no error is shown after clicking "Apply", try clicking on "Reset Credentials" or type "docker logout" in a (bash) terminal.

Alternative "fixes" that have worked, are to restart docker by right-clicking on the "whale" icon in the system tray and/or restart your computer. It is best to quit any running process before you restart your computer (i.e., press q and Enter in the launch menu)

## Optional

To install python3 on Windows using **chocolatey**, open a CMD terminal **as administrator** and copy-and-paste the code below. Note: You may have to right-click to get a copy-and-paste menu for the terminal

```bash
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin";
```

Now close the terminal and open a new CMD terminal **as administrator** and copy-and-paste the code below:

```bash
choco install python3;
```