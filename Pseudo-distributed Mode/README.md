# Hadoop Pseudo-distributed Mode
The project is goint to set up a small Hadoop node on local machine using VMware Workstation & Ubuntu 20.04.

## Steps:
**1.Update the instance:**
<br>
```shell
sudo apt-get update && sudo apt-get -y dist-upgrade 
```

**2.Install Java on all instances:**
<br>
```shell
sudo apt-get install openjdk-8-jdk-headless
```

**3.Install Hadoop(2.7.3) on all instances**
<br>
```shell
wget http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
tar xvf hadoop-2.7.3.tar.gz
```

**4.Modify 'hadoop-env.sh', add Java_Home**
<br>`sudo vim ~/hadoop-2.7.3/etc/hadoop/hadoop-env.sh`
```shell
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
**5. Add Hadoop_Home to environmental variable**
<br>`sudo vim /etc/profile`
<br>add
```shell
export HADOOP_HOME=~/hadoop-2.7.3  # put Hadoop install path here
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
<br>then
`sudo source /etc/profile`<br>
**6. Create a nickname for the instance**
`sudo vim /etc/hosts`
<br> add one line at the top, from here, the ip addresses in the following configurations are replaced with `node`
```she
192.168.18.128  node1  # replace the ip address here
```

**7.Modify hdfs-site.xml**
`vim ~/hadoop-2.7.3/etc/hadoop/hdfs-site.xml`
<br>add configuration
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

**8. Modify yarn-site.xml**
<br>```vim ~/hadoop-2.7.3/etc/hadoop/yarn-site.xml```
<br>add configuration
```xml
<configuration>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>node1</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

**9.Modify core_site.xml**
<br>```vim ~/hadoop-2.7.3/etc/hadoop/core_site.xml```
<br>add configuration
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>node1:9000</value>
  </property>
</configuration>
  ```
  
**10.Copy mapred-site.xml.template to mapred-site.xml and modify mapred-site.xml**
```shell
cp mapred-site.xml.template mapred-site.xml
vim mapred-site.xml
```
<br>add configuration
```xml
configuration>
  <!-- run mapreduce on yarn -->
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

**11. Setup slaves**
```shell
vim ~/hadoop-2.7.3/etc/hadoop/slaves
```
create a file with only 1 line
```xml
node1
```

## Set-up passwordless SSH login between Namenode and the three Datanodes
**1. Generate public key**
```shell
ssh-keygen
```
**2. Copy the public key to the node**
```shell
ssh-copy-id [user]@host  # replace user and host ip address here
```
**3. Restart**
```shell
sudo reboot
```

## Starting Hadoop 
**1.format Hadoop**
<br>`
hadoop namenode -format
`
<br>**2.start the cluster**
<br>``
start-all.sh
``
<br>the command will run as `start-yarn.sh` and `start-dfs.sh`
<br>**3.check the running status**
<br>`jps`
<br> or open `namenode's ip:50070` in a browser

