# meta-sed-rzg2e
meta layer for icore rzg2e for sed lcd board

## Building environment
Ubuntu should be used as the Linux Host PC OS since the Yocto Project Quick Start specifies Ubuntu as one
of the supported distributions. 


### Step 1 Ubuntu Host Preparation

Recommended Ubuntu 18.04 LTS (64-bit) as Host PC.

In that case, you can install the required packages by using the commands
below.

    sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
    build-essential chrpath socat libsdl1.2-dev xterm cpio python python3 \
    python3-pip python3-pexpect xz-utils debianutils iputils-ping libssl-dev

### Step 2 download of required files

 create deploy directory
 
    export WORK=~/sed-yocto-bsp
    mkdir -p  ${WORK}
    
 create deploy directory
    
    cd ${WORK}
    git clone git://git.yoctoproject.org/poky
    git clone git://git.linaro.org/openembedded/meta-linaro.git
    git clone git://git.openembedded.org/meta-openembedded
    git clone https://github.com/renesas-rz/meta-rzg2.git
    git clone http://git.yoctoproject.org/cgit.cgi/meta-gplv2
    git clone https://github.com/meta-qt5/meta-qt5.git
    
    
    git clone clone engicam-stable/meta-engicam-rzg2
    

### Step 3 checkout
  
    cd ${WORK}/poky
    git checkout -b tmp 7e7ee662f5dea4d090293045f7498093322802cc
    cd ${WORK}/meta-linaro
    git checkout -b tmp 75dfb67bbb14a70cd47afda9726e2e1c76731885
    cd ${WORK}/meta-openembedded
    git checkout -b tmp 352531015014d1957d6444d114f4451e241c4d23
    cd ${WORK}/meta-gplv2
    git checkout -b tmp f875c60ecd6f30793b80a431a2423c4b98e51548
    cd ${WORK}/meta-qt5
    git checkout -b tmp c1b0c9f546289b1592d7a895640de103723a0305
    cd ${WORK}/meta-rzg2
    git checkout -b tmp BSP-1.0.8


### Building Instruction

Build BSP using Yocto


#### Ubuntu Host Preparation

### Set up the repository

We first make sure that no old Docker installations are hanging around in our Ubuntu system.

    $ sudo apt-get remove docker docker-engine docker.io \
      containerd runc
  
Docker requires a few additional Linux packages to function properly.

    $ sudo apt-get update

    $ sudo apt-get install apt-transport-https ca-certificates curl \
      gnupg-agent software-properties-common

Add Docker’s official GPG key:

    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add 

Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88, by searching for the last 8 characters of the fingerprint.

    $ sudo apt-key fingerprint 0EBFCD88

    pub   rsa4096 2017-02-22 [SCEA]
          9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]

We make Docker’s Linux package repository known to our computer. The command lsb_release -cs returns the codename of the Linux distribution, which yields bionic for Ubuntu 18.04.

    $ sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable"

Install Docker Engine:

    $ sudo apt-get update
    $ sudo apt-get -y install docker-ce

The last command installs the server and client of Docker CE, starts the server and creates a group docker. If we add our user name to the docker group, we don’t have to run each Docker command as root.

    $ sudo usermod -a -G docker "$(whoami)"

## First build

Clone the repository:

    $ git clone https://github.com/rfacch74/rototype-docker-bsp.git

Launch the script to prepare the environment:

    $ cd ~/rototype-docker-bsp
    $ ./prepare_rototype_bsp nxp gatesgarth

at the end of procedure under /yocto folder you can found all source code.


## Set enviroment variales

Enter Yocto folder and launch the script setting enviroment variables:

	DISTRO=<distro name> MACHINE=<machine name> source imx-setup-release.sh -b <build dir>

where ``<machine-name>`` corresponds to the module for which the operative system image will be compiled and ``<build-dir-name>`` is the bulding directory name chosen by the user, as in the following example:

    $ DISTRO=ecp-imx-wayland MACHINE=imx8mq-ecp source imx-setup-release.sh -b build-rototype-ecp

where a directory named ``build`` is created and enviroment variables are set to compile images for the module ``Rototype ECP MX8MQ``. Please notice that the available ``<machine-name>`` correspond to the names of the relative configuration files in ``/sources/meta-rototype-ecp/conf/machine``.

If the build directory already exists due to previous compilations it will be required to set the enviroment variables only executing the command:

    $ source setup-environment build

## Configure yocto layers

Add with bitbake the meta-rototype-ecp layer to the image layers:

    $ bitbake-layers add-layer ../sources/meta-rototype-ecp

## Compile and flash image on sdcard

Compile the desired image with bitbake using the command (in this example we compile the recipe ``rototype-evaluation-image.bb`` for ``ROTOTYPE ECP MX8MQ ``):

    $ bitbake rototype-evaluation-image

Once the image is compiled it will be possible to find in the build directory a deploy folder with the image files. The relative path to this folder from the yocto directory will be:

    $ tmp/deploy/images/imx8mq-ecp
