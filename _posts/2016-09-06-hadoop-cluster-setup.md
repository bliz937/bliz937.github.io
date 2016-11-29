---
layout: post
title:  "Creating a hadoop cluster"
date:   2016-09-06 13:00:00 +0200
description: "A quick way to get started with hadoop."
tags: hadoop cluster parallel computing apache setup sysadmin
---

For my operating systems module, we needed to install and code for [Apache Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop).
I wanted to try and script the install process as much as possible, and so I have done that. I have, however, managed to break my machine with this script, but the error that caused it has been fixed. Regardless, you are to **run this script at your own risk**. This post is targeted at ```hadoop 2.7.2```.

The script needs some configuration, namely the variables and application paths that can be found at the top.
I configured this script for Arch Linux, so do have a look that the configuration is correct. To find an application path, you can use ```which```, eg: ```which tar```. If the result is not the same as in the script, change the script to match the output. If there is no result, install the package.

{% gist cab29ba333e3c10f1f9bd4e46af043c4 %}

Run the script as root - reason how I broke my machine and why you should do this at **your own risk**.

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

After hadoop is downloaded, it will be extracted to ```/opt/hadoop/```, the hadoop user will be created and a local job will run.

![Hadoop extracting and running local job](/assets/2016/08/hadoop_extract_and_run.png)

At the end of the job, the result will be displayed and the script will pause for user input. Hit enter to continue.

![Hadoop local job result](/assets/2016/08/hadoop_local_job.png)

The second pause, the script will ask that the root and hadoop account to have public key authentication. This basically involves running ```ssh-keygen``` and copying ```id_rsa.pub``` to ```authorized_keys``` in the ```~/.ssh/``` directory. There are a few guides online on this if you are not familiar with the process.

![Hadoop enable public key authentication](/assets/2016/08/hadoop_ssh_keys.png)

After that, the cluster configuration is done and the hadoop file system is formatted.

![Hadoop datanode](/assets/2016/08/hadoop_finish.png)

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


#### Starting the cluster

On master, to start the hdfs, run:

```bash
start-dfs.sh
```

```jps``` should look similar to

```
[hadoop@nyancat shev]$ jps
7954 NameNode
8085 DataNode
9769 Jps
8585 SecondaryNameNode
```

To start the job tracker, run:

```bash
start-yarn.sh
```

```jps``` should look similar to

```bash
[hadoop@nyancat shev]$ jps
8771 ResourceManager
9060 NodeManager
9773 Jps
```

Lastly, starting the history server:

```bash
mr-jobhistory-daemon.sh start historyserver
```

```bash
[hadoop@nyancat shev]$ jps
10269 JobHistoryServer
9231 Jos
```

#### stopping the cluster

On master

```bash
mr-jobhistory-daemon.sh stop historyserver
stop-dfs.sh
stop-yarn.sh
```
```jps``` should look similar to:

```bash
[hadoop@nyancat ~]$ jps
13958 Jps
```

#### Running jobs

You can run jobs by running the following commands

```bash
hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar /input/ /output/
```

Where ```/input``` and ```/output``` are directories on the hdfs.

You can use ```copyFromLocal``` to copy files form the local file system to the hdfs.

Example: ```hdfs dfs -copyFromLocal ./foo /input```

More hdfs commands can be found here: [https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html)

#### Stats

hdfs: [http://localhost:50070/](http://localhost:50070/)

![Hadoop web datanode](/assets/2016/08/hadoop-web-storage.png)

Resource manager:  [http://localhost:8088/](http://localhost:8088/)

![Job tracker](/assets/2016/08/yarn_job.png)

JobHistoryServer:  [http://localhost:19888](http://localhost:19888)

![Job history server](/assets/2016/08/history_server.png)

#### Diagnostics

I had a case where a slave data node was not working. To diagnose the cause, I simply ran:

```bash
hadoop datanode
```

Observing the output helped to find the problem.

#### Extra reading
* [readthedocs.io](https://doctuts.readthedocs.io/en/latest/hadoop.html)

* [dwbi cluster setup](https://dwbi.org/etl/bigdata/183-setup-hadoop-cluster)

* [Yarn](https://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/YARN.html)

* [Michael-Noll single node setup](http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-single-node-cluster/)

* [Michael-Noll cluster setup](http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-multi-node-cluster/)

* [Standalone Operation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)

* [Pseudo-Distributed Operation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)

* [Cluster setup](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)
