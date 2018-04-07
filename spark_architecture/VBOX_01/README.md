
# VBOX_01

tutorials used:
* main spark tutorial: https://www.davidadrian.cc/posts/2017/08/how-to-spark-cluster/
* main ubuntu virtualbox networking: https://www.youtube.com/watch?v=S7jD6nnYJy0
* secondary spark tutorial: http://jaehyeon-kim.github.io/2016/02/Spark-Cluster-Setup-on-VirtualBox.html



generic virtual box ubuntu setup
* Ubuntu 16.04
* swap (512 Mb), / (10,000 Mb), /home (Remaining Mb)
* install guest additions, add current user to vboxsf group (shared folders), turn on bidirectional clipboard
* On virtualbox: File >> Host Network Manager:
	- Adapter tab: configure adapter manually: 192.168.56.1 (IPv4) & 255.255.255.0 (Mask)
	- DHCP Server tab: "Enable Server" should be unchecked
* shut down the VM
* while VM is down - right click the VM in the virtualbox menu >> Settings >> Network
	- Adapter 1 should be enabled and set to NAT (this enables you to access the internet from your VM. Neat.)
	- Adapter 2 should be enabled and set to "Host Only Adapter" - then select the one you set up above (there should be one there by default)


## general setup
	
	sudo apt-get update
	sudo apt-get upgrade         // this takes a while
	sudo apt-get install vim
	sudo apt-get install gdebi   // rstudio is a .deb


## install and check java

	sudo apt-get install openjdk-8-jdk
	java -version         // make sure it works and path is set up and all
	// at this point, I don't have $JAVA_HOME set up, but path still works?


## download and install spark

The point of this is to get set up and running as quickly as possible. Putting the spark binary in the current user's home directory (~)

	cd ~
	curl -O https://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz
	tar zxvf spark-2.2.0-bin-hadoop2.7.tgz


## edit bashrc

	sudo vim .bashrc
	
add this line to the end: 

	export SPARK_HOME="/home/taylor/spark-2.2.0-bin-hadoop2.7/"

save that file

	source .bashrc  // or start another terminal


## test spark installation

	cd spark-2.2.0-bin-hadoop2.7
	bin/pyspark 
	
you'll see a few error things and then you'll see the Spark ascii art


## optional -- this is where we would install Hadoop / HDFS if we were doing that


## Install R stuff

	sudo apt-get install r-base
	R
	// In R prompt...
	// this will install in personal location unless we use
	// choose a mirror - I chose revolution analytics in Texas
	install.packages("dplyr")       
	install.packages("lubridate")
	install.packages("ggplot2")
	install.packages("data.table")
	install.packages("anytime")
	install.packages("assertthat")
	// OS dependencies for httr:
	sudo apt-get install libcurl4-openssl-dev
	sudo apt-get install libssl-dev
	install.packages("httr")  // this one always causes problems
	install.packages("tidytext")
	// OS dependencies for tm package
	sudo apt-get install libxml2-dev
	install.packages('devtools')
	library(devtools)
	slam_url <- "https://cran.r-project.org/src/contrib/Archive/slam/slam_0.1-37.tar.gz"
	install_url(slam_url)
	install.packages("tm")
	
	// Spark-dependent R packages
	install.packages("SparkR")     // this is the primary API to spark
	install.packages("sparklyr")   // dplyr map to SparkSQL (and libML)
	install.packages("rsparkling") // H2O's extension of sparklyr
	// rsparkling will download and install some .jar files
	
	
	// install Rstudio for Ubuntu
	cd ~  // I'm just in my home directory here
	wget https://download1.rstudio.org/rstudio-xenial-1.1.442-amd64.deb
	sudo apt install ./rstudio-xenial-1.1.442-amd64.deb
	
	
## Install Python stuff

Python will likely already be included on the Linux box, as it ships with most distros by default

	// python 2
	which python      // path to python interpreter/executable
	python --version  // check the version of python
	
	// python 3
	which python3
	python3 --version 
	

Anaconda is another good tool for managing Python environments. It is worth noting a few pieces of commonly confused terms:

* Anaconda  - the full blown anaconda environment with ~150 packages included and many other programs like spyder (and maybe the graphical interface thing?)
* Miniconda - bare-bones conda installation for those who would rather install things as they need them and prefer to work from the command line
* Conda     - the package management system that ships with either of the above options. this is where you create environments, switch between them, and do your installs and what-not. Each environment will be given it's own site-packages directory.
* https://stackoverflow.com/questions/45421163/anaconda-vs-miniconda/47789738 

	
I'm fairly certain that if you download the miniconda Python 3.x (3.6) version, then you'll be able to generate Python 2.x (2.7) environments. Better to go with the newest version and hope for backwards compatability than go with the older one and hope it contains the new features. 

	
	// just installing into /~ for now
	wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
	bash Miniconda3-latest-Linux-x86_64.sh
	
	
It's installing in /home/taylor/miniconda3.
I'll accept that as the default.
	
"Pre-pend the miniconda3 install location to PATH in your /home/taylor/.bashrc?"
I'll say yes to this despite the default being no. I think that is fine since I don't have any pre-existing Python projects on this machine.

	source ~/.bashrc  // reload my profile's bashrc for PATH change
	which python      // /home/taylor/miniconda3/bin/python
	python --version  // Python 3.6.4 :: Anaconda, Inc.

	
	// I'd now like to create a conda env with some scientific / ML / spark-related packages in it <------------------------------------------------
	// show how to create the conda env with specific version of python
	// activation / deactivation of environment

	conda update -n base conda                 // update conda
	conda create --name sparktest python=3.6   // create new env
	which python                               // /home/taylor/miniconda3/envs/sparktest/bin/python
	source activate sparktest
	conda install PySpark
	conda install scikit-learn
	conda install seaborn
	conda install jupyter
	jupyter notebook  // run the jupyter notebook
	// CTRL + Z to end it (and close browser)
	ps  // find running processes
	kill -9 <PID of Jupyter process> // to kill the jupyter process
	
	
# Now create export of this machine - this is base_ubuntu_datascience

## Things I didn't include in this last snapshot that I wish I had...

* sudo apt-get install openssh-server   // the spark launch scripts require password-less ssh with keys
* installation of "normal" java, instead of the open jdk thing. or figure out some way to install rJava in R
	- This would be useful for several R Packages: hive, RJDBC, rJava, DBI



	
	

## Networking changes - this should be done per machine

I have three machines to configure:

1. Master
* Virtualbox name: SPK01-master
* IP: 192-168-56-100
* hostname: spk-master
2. Worker1
* Virtualbox name: SPK01-worker-1
* IP: 192-168-56-101
* hostname: spk-worker-1
3. Worker2
* Virtualbox name: SPK01-worker-2
* IP: 192-168-56-102
* hostname: spk-worker-2


### Master node networking changes

change host name in /etc/hostname

	sudo vim /etc/hostname
	// overwrite the contents with new hostname (SPK01-master)
	// :wq!
	

change host name in /etc/hosts

	sudo vim /etc/hosts
	// change the thing under localhost (aligned with 127.0.1.1) to new hostname
	

now check name of second adapter (should be enp0s8)

	ifconfig -a
	
Now we need to make this machine have a static IP address. Append this to the end of the file at /etc/network/interfaces :


	auto enp0s8
	iface enp0s8 inet static
	address 192.168.56.100
	netmask 255.255.255.0
	
	
The above text in that file will be the same for all machines except for the actual ip address - which we'll increment by 1.

 
 
## setting up passwordless ssh (this is for cluster launch scripts)

First make sure to edit the /etc/hosts to reflect the host names of the worker nodes, so add this below the other IPv4 stuff:

	192.168.56.101    SPK01-worker-1   // this is what I chose to name my worker-1 host
	192.168.56.102    SPK01-worker-2

This is for the master node to be able to automatically spin up the workers with a single script/config file

https://askubuntu.com/questions/46930/how-can-i-set-up-password-less-ssh-login 


On all nodes (if not done already)

	sudo apt-get install openssh-server
	

on master node...

	ssh-keygen  // hit enter three times, don't use a password with the key
	ssh-copy-id taylor@192.168.56.101  // user@address of worker-1
	//enter password of worker-1 box
	ssh taylor@192.168.56.101      // now this should work with no password
	
	// odd, but ssh taylor@SPK01-worker-1 doesn't work even though
	// it is set up in the /etc/hosts file and I can ping it...
	// had to explicitly copy id to that host as well
	ssh-copy-id taylor@SPK01-worker-1
	
	// do this for all workers
	// test ssh
	ssh taylor@SPK01-worker-1   // make sure this works
	
on master node...

	// go into spark directory (for me: ~/spark-2.2.0-bin-hadoop2.7/)
	cd conf
	
	// make a copy of the slaves template and edit it
	cp slaves.template slaves
	vim slaves

	// now comment out the "localhost" line (I want master to just be a master for now, not worker)
	// then add these two lines below
	SPK01-worker-1
	SPK01-worker-2
	
	
	// on master node... 
	// hostname and port is really picky, so make a copy of the spark-env.sh file and fill in those config items
	cd <SPARK_HOME>/conf
	cp spark-env.sh.template spark-env.sh   //create the copy
	vim spark-env.sh
	
	
	// add these two items somewhere that isn't commented out - this is easier as an ip address I've found...
	SPARK_MASTER_HOST=192.168.56.100
	SPARK_MASTER_PORT=7077
	
	
	// send out the config file to the worker nodes as well - can we script this?
	scp spark-env.sh taylor@SPK01-worker-1:~/spark-2.2.0-bin-hadoop2.7/conf/spark-env.sh
	scp spark-env.sh taylor@SPK01-worker-2:~/spark-2.2.0-bin-hadoop2.7/conf/spark-env.sh
	
	
	// on master node
	cd $SPARK_HOME
	./sbin/start-all.sh   // SUCCESS!
	
	
	// circling back around and adding "localhost" to the conf/slaves file to see if we can add
	// the master as a third slave node itself
	// this worked! 
	
	
	
## Manual start of the spark cluster:

	// on master:
	./sbin/start-master.sh -h 192.168.56.100
	
	// on slave:
	./sbin/start-slave.sh 192.168.56.100:7077



	
## Rstudio server:

	// I think this can be master-node only?
	cd ~
	wget https://download2.rstudio.org/rstudio-server-1.1.442-amd64.deb
	sudo apt install ./rstudio-server-1.1.442-amd64.deb

	// access it on HOST machine browser (windows machine) at this site:
	// http://192.168.56.100:8787/
	
	
	// in R script (logged into RStudio Server on Windows machine browser)
	
	
	sparkR.session(master="spark://192.168.56.100:7077")  # <-- I think this is how to connect
	
	
	// so I have spark up and running and it works fine	
	// I'm really interested in all of the distributed "apply" family functions - especially since you can
	// use your own user-defined function, just like the original sapply / lapply / mapply
	
	// one thing that was a little concerning about SparkR was that the CPUs of the workers
	// went up to a steady ~70-80% immediately whenever I initialized teh SparkR session with app name.
	// I'll be curious to see if this happens with pyspark as well.
	
	
	See **abalone_modeling_test.R** for my quick script to test a 
	
	
	
## Zeppelin 
	
Zeppelin only supported for Ubuntu 14.04 (I'm on 16.04) and Oracle Java 1.7 (I have openjdk-8). So we'll see.
	
Zeppelin is going to require us to set a JAVA_HOME environment variable. We'll just do it in ~/.bashrc.

	echo $JAVA_HOME  // make sure it doesn't exist

If we installed the openjdk-8, then we can run this to find where the java executable is...

	which java  
	// /usr/bin/java
	
I don't think that's where we'll want to set our $JAVA_HOME variable to though.. this is a symbolic link, let's figure out where it really is

	readlinke -f $(which java)
	// /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
	
	
Can't seem to figure out how to find the path to the download. Just download on the host machine and use git bash to scp it to "remote" VBox VM.


	
	
	
	
