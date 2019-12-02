# Hadoop-setup
The project is goint to set up a small Hadoop cluster on AWS, including 1 namenode and 3 datanodes.

**EC2 instance:** Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - t2.micro (free tier)<br>
**Security Group:** Add inbound rules: Allow all trffic, all protocol, all port range from My IP

## All Nodes:
**1.Update the instance:**
<br>
```shell
ubuntu@ip-10-0-1-161:~$ sudo apt-get update && sudo apt-get -y dist-upgrade 
```

**2.Install Java on all instances:**
<br>
```shell
sudo apt-get -y install openjdk-8-jdk-headless
```

**3.Install Hadoop(2.7.3) on all instances**
<br>
```shell
wget http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
tar xvzf hadoop-2.7.3.tar.gz
```

**4.Set up Java_Home**
```shell
sudo vim ~/hadoop-2.7.3/etc/hadoop/hadoop-env.sh
```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

**5.Update core_site.xml**
<br>```sodu vim ~/hadoop-2.7.3/etc/hadoop/core_site.xml```
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value><nnode>:9000</value>
  </property>
</configuration>
  ```

## Set-up passwordless SSH login between Namenode and the three Datanodes
**1.Namenode: generate public key**
<br>use default /home/ubuntu/.ssh/id_rsa
```shell
namenode> ssh-keygen
```
**2.Datanode: copy the id_rsa to each datanode**
<br>we cannot scp the id_rsa file directly to each datanode, what we can do is to create a key.txt in each datanode under /home/ubuntu/.ssh, and then mannually attach the key to it.
```shell
datanode> cd /home/ubuntu/.ssh
datanode> vim key.txt(attach the key)
```
**3.Datanode: attach the key to each datanode's authorized_keys**
```shell
datanode> cat key.txt >> ~/.ssh/authorized_keys
```
**4.Namenode: Setup SSH Config**
```shell
namenode> cd /home/ubuntu/.ssh
namenode> vim config
```
replace `<nnode>,<dnode1>,<dnode2>,<dnode3>` with EC2 public DNS
```
Host namenode
  HostName <nnode>
  User ubuntu
  IdentityFile ~/.ssh/id_rsa

Host datanode1
  HostName <dnode1>
  User ubuntu
  IdentityFile ~/.ssh/id_rsa

Host datanode2
  HostName <dnode2>
  User ubuntu
  IdentityFile ~/.ssh/id_rsa

Host datanode3
  HostName <dnode3>
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
```
**5.Namenode: test passwordless SSH connection**
```shell
namenode> ssh namenode
namenode> ssh datanode1
namenode> ssh datanode2
namenode> ssh datanode3
```
## Setup Namenode(under ~hadoop-2.7.3/etc/hadoop/)
**1.Setup hdfs-site/xml**
<br>add configuration
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>3</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///usr/local/hadoop/hdfs/data</value>
  </property>
</configuration>
```
**2.Setup mapred-site.xml**
<br>copy the file `mapred-site.xml.template` and name it `mapred-site.xml`
```shell
scp mapred-site.xml.template mapred-site.xml
```
add configuration, and replace `<namenode>` with Namenode public DNS
```xml
<configuration>
  <property>
    <name>mapreduce.jobtracker.address</name>
    <value><namenode>:54311</value>
  </property>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
  ```
**3.Setup yarn-site.xml**
<br>add configuration
```xml
<configuration>
  <!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value><nnode></value>
  </property>
</configuration>
  ```
**4.Setup master nodes**
```shell
sudo vim masters
```
add one line, replace <namenode> with Namenode's public DNS
<br>`<namenode>`

<br>**5.Setup slaves**
```shell
sudo vim slaves
```
replace `<datanode1>`,`<datanode2>`,`<datanode3>` with their public DNS
<br>
```shell
<datanode1>
<datanode2>
<datanode3>
```

## Setup Datanode(under ~hadoop-2.7.3/etc/hadoop/)
**1.Setup hdfs-site.xml**
<br> add configuration
```xml
<configuration>
  <property>
  <name>dfs.replication</name>
  <value>3</value>
</property>
<property>
  <name>dfs.datanode.data.dir</name>
  <value>file:///usr/local/hadoop/hdfs/data</value>
</property>
</configuration>
```

## Starting the Hadoop cluster
