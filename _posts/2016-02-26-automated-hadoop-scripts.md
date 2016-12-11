---
layout: post
title:  "Automated Scripts for setting up Hadoop Environment"
date:   2016-02-26
desc: "Automated scripts for setting up Hadoop Environment onto either Single or Multi-node cluster."
keywords: "Hadoop,installing, deployment"
categories: [bigdata]
tags: [bigdata]
icon: fa-code
---

This post describes how to set up a Hadoop environment in a single node.
First thing needs to be done is to add hostname and host's IP adress into /etc/hosts file

#For example 
``` shell
cat /etc/hosts >> 192.168.1.25 hostname
```

After that the bash code below needs to be pasted into an sh file and run (sh ./filename)

``` shell
#!/bin/bash
VERSION=hadoop-2.7.1
apt-get install python-software-properties
add-apt-repository ppa:webupd8team/java
apt-get update
apt-get install oracle-java8-installer
apt-get install ssh
#download hadoop, untar, put in /usr/local
cd ~/Downloads
wget http://ftp.itu.edu.tr/Mirror/Apache/hadoop/common/hadoop-2.7.1/hadoop-2.7.1.tar.gz
tar -xzf "$VERSION".tar.gz
mv  $VERSION /usr/local

#create user and group
addgroup hadoop
adduser --ingroup hadoop hduser
echo user created........................................
# app folder; who uses this ????
mkdir -p /app/hadoop/tmp
chown hduser:hadoop /app/hadoop/tmp
chmod 755 /app/hadoop/tmp

#modify hadoop-env
cd /usr/local/$VERSION/etc/hadoop
echo "export JAVA_HOME=/usr/lib/jvm/java-8-oracle/" >> hadoop-env.sh
echo "export HADOOP_OPTS=-Djava.net.preferIPv4Stack=true" >> hadoop-env.sh

#get configuration files
rm core-site.xml
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/conf/core-site.xml
rm mapred-site.xml.template
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/conf/mapred-site.xml.template
rm hdfs-site.xml
wget https://raw.githubusercontent.com/MuhammedGit/MuhammedGit/Muho/conf/hdfs-site.xml

# chmod
cd /usr/local
ln -s $VERSION hadoop
chown -R hduser:hadoop $VERSION
echo .......ssh connection 
su - hduser -c "echo | ssh-keygen -t rsa -P \"\""
cat /home/hduser/.ssh/id_rsa.pub >> /home/hduser/.ssh/authorized_keys
su - hduser -c "ssh -o StrictHostKeyChecking=no localhost echo "# login once, to add to known hosts
```

So, this script downloads, decompressses hadoop file, changes the neccesary configuration files. 
The main Hadoop files will be located under /usr/local/
