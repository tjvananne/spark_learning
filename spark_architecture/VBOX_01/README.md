
# VBOX_01

tutorials used:
* main spark tutorial: https://www.davidadrian.cc/posts/2017/08/how-to-spark-cluster/
* main ubuntu virtualbox networking: https://www.youtube.com/watch?v=S7jD6nnYJy0
* secondary spark tutorial: http://jaehyeon-kim.github.io/2016/02/Spark-Cluster-Setup-on-VirtualBox.html



generic virtual box ubuntu setup
* Ubuntu 16.04
* swap (512 Mb), / (10,000 Mb), /home (Remaining Mb)
* install guest additions, add current user to vboxsf group (shared folders), turn on bidirectional clipboard
* reboot


##general setup
	
	sudo apt-get update
	sudo apt-get upgrade      // this takes a while
	sudo apt-get install vim


## install and check java

	sudo apt-get install openjdk-8-jdk
	java -version         // make sure it works and path is set up and all


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



