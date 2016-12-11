---
layout: post
title:  "Automated Scripts for setting up Hadoop Environment for Multi-node cluster"
date:   2016-02-27
desc: "Automated scripts for setting up Hadoop Environment onto either Single or Multi-node cluster."
keywords: "Hadoop,installing, deployment"
categories: [bigdata]
tags: [bigdata]
icon: fa-code
---

This post describes how to set up a Hadoop environment in a Multi-node.
First thing needs to be done is to add hostname and host's IP adress into each others /etc/hosts files
This step needs to be do on both nodes.

#For example 
/etc/hosts
192.168.1.25 master
192.168.1.26 slave

On Both nodes
After that the bash code below needs to be pasted into an sh file and run (sh ./filename)

``` bash
#!/bin/bash
VERSION=hadoop-2.7.2
apt-get install python-software-properties
add-apt-repository ppa:webupd8team/java
apt-get update
apt-get install oracle-java8-installer
apt-get install ssh
#download hadoop, untar, put in /usr/local
cd ~/Downloads
wget http://ftp.itu.edu.tr/Mirror/Apache/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz

tar -xzf "$VERSION".tar.gz
mv  $VERSION /usr/local

#create user and group
addgroup hadoop
adduser --ingroup hadoop hduser
sudo adduser hduser sudo

mkdir -p /app/hadoop/tmp
chown hduser:hadoop /app/hadoop/tmp
chmod 755 /app/hadoop/tmp

cd /usr/local
mv $VERSION hadoop
chown -R hduser:hadoop hadoop
#modify hadoop-env
cd /usr/local/hadoop/etc/hadoop

#echo "export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64" >> hadoop-env.sh
echo "export JAVA_HOME=/usr/lib/jvm/java-8-oracle" >> hadoop-env.sh
echo "export HADOOP_OPTS=-Djava.net.preferIPv4Stack=true" >> hadoop-env.sh

rm core-site.xml
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/Multi-Node/conf/core-site.xml

wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/Multi-Node/conf/mapred-site.xml
rm hdfs-site.xml
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/Multi-Node/conf/hdfs-site.xml
rm yarn-site.xml
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/Multi-Node/conf/yarn-site.xml

```

After these steps from master node run the code below
``` bash
ssh-copy-id -i $HOME/.ssh/id_rsa.pub hduser@slave
```

So, this script downloads, decompressses hadoop file, changes the neccesary configuration files.
With the given configurations Hadoop is now able to process distributedly on both nodes.
The main Hadoop files will be located under /usr/local/
