---
layout: post
title:  "Creating a hadoop cluster"
date:   2016-09-06 13:00:00 +0200
description: "A quick way to get started with hadoop."
tags: hadoop cluster parallel computing apache setup sysadmin
---

For my operating systems module, we needed to install and code for [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop).
I wanted to try and script as much as this as possible.

I created the script below, and to an extent have gotten

* [Standalone Operation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
* [Pseudo-Distributed Operation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
* [Cluster setup](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)

I have, however, managed to break my machine with this script, but the error that caused it has been fixed. Regardless, you are to **run this script at your own risk**. This post is targeted at ```hadoop 2.7.2```.

The script needs some configuration, namely the variables and application paths that can be found at the top.
I configured this script for Arch Linux, so do have a look that the configuration is correct. To find application path, you can use ```which```, eg: ```which tar```. If the result is not the same as in the script, change the script to match the output.

{% gist cab29ba333e3c10f1f9bd4e46af043c4 %}

Run the script as root (reason how I broke my machine and why you should do this at your own risk).

```bash
sudo su
cd
wget https://gist.githubusercontent.com/bliz937/cab29ba333e3c10f1f9bd4e46af043c4/raw/hadoop-setup.sh
chmod +x hadoop-setup.sh

# edit the file for your needs (variables at the top)
# vim hadoop-setup.sh

./hadoop-setup.sh
```

![Downloading hadoop](/assets/2016/08/hadoop-download.png)

During the execution of the script, there will be times where the script will wait for user input with some message. The first one is the result from running a job standalone. Hitting enter will continue the script.

![Result of standalone hadoop run](/assets/2016/08/hadoop-standalone.png)

The second pause, the script will ask that the root and hadoop account to have public key authentication. This basically involves running ```ssh-keygen``` and copying ```id_rsa.pub``` to ```authorized_keys``` in the ```~/.ssh/``` directory. There are a few guides online on this if you are not familiar with the process.

![Hadoop enable public key authentication](/assets/2016/08/hadoop-pubkey.png)

Next the datanode and namenode service starts, and the script pauses with a message to view ```http://localhost:50070``` in the web browser.

![Hadoop datanode](/assets/2016/08/hadoop-namenode.png)

```http://localhost:50070``` should look similar to this:

![Hadoop web datanode](/assets/2016/08/hadoop-web-storage.png)


The next pause the script will inform you to have a look at the pseudo cluster job results which can be found above.

![Hadoop pseduo cluster](/assets/2016/08/hadoop-pseudo.png)

The last step in the script configures the node's master job tracker and hdfs.

![Hadoop script finish](/assets/2016/08/hadoop-finish.png)

The script ends of saying what still needs to be done.

#### On master node

From the **master node**, copy your public key to all the slave nodes.

```bash
su hadoop
ssh-copy-id 172.16.39.38
```

where 172.16.39.38 is the **slave IP**.

Edit ```/etc/hosts``` and insert all the **slave** IP's and hostnames, eg:

```
172.16.39.38  node1
```

Add all the slave hostname's to ```/opt/hadoop/etc/hadoop/slaves```.


#### On slave nodes

On the **slaves**, add the **master**'s IP and hostname in ```/etc/hosts```.

On the **slaves**, run

```bash
hadoop-daemon.sh start datanode
hadoop-daemon.sh start namenode
```

Running ```jps``` on the slaves, you should see

```
hadoop@node1:~$ jps
15360 DataNode
15474 Jps
```

#### Starting the cluster

On master

```bash
hadoop-daemon.sh --config /opt/hadoop/etc/hadoop/ --script hdfs start namenode
hadoop-daemon.sh --config /opt/hadoop/etc/hadoop/ --script hdfs start datanode
start-dfs.sh
yarn-daemon.sh --config /opt/hadoop/etc/hadoop/ start resourcemanager
yarn-daemon.sh --config /opt/hadoop/etc/hadoop/ start nodemanager
start-yarn.sh
mr-jobhistory-daemon.sh --config /opt/hadoop/etc/hadoop/ start historyserver
```

```jps``` should look similar to

```
[hadoop@nyancat shev]$ jps
7954 NameNode
8771 ResourceManager
9060 NodeManager
8085 DataNode
9769 Jps
8585 SecondaryNameNode
9674 JobHistoryServer
```

#### stopping the cluster

On master

```bash
hadoop-daemon.sh --config /opt/hadoop/etc/hadoop/ --script hdfs stop namenode
hadoop-daemon.sh --config /opt/hadoop/etc/hadoop/ --script hdfs stop datanode
stop-dfs.sh
yarn-daemon.sh --config /opt/hadoop/etc/hadoop/ stop resourcemanager
yarn-daemon.sh --config /opt/hadoop/etc/hadoop/ stop nodemanager
stop-yarn.sh
mr-jobhistory-daemon.sh --config /opt/hadoop/etc/hadoop/ stop historyserver
```
```jps``` should look similar to:

```bash
[hadoop@nyancat ~]$ jps
13958 Jps
```

#### Stats

hdfs: [http://localhost:50070/](http://localhost:50070/)

#### Extra reading
[HDFS commands](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html)

[Yarn](https://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/YARN.html)

[Michael-Noll single node setup](http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-single-node-cluster/)

[Michael-Noll cluster setup](http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-multi-node-cluster/)
