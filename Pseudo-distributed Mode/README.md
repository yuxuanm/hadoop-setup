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
sudo apt-get -y install openjdk-8-jdk-headless
```

**3.Install Hadoop(2.7.3) on all instances**
<br>
```shell
wget http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
tar xvzf hadoop-2.7.3.tar.gz
```

**4.Modify 'hadoop-env.sh', add Java_Home**
```shell
sudo vim ~/hadoop-2.7.3/etc/hadoop/hadoop-env.sh
```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

**5. Add Hadoop_Home to environmental variable**
```shell
sudo vim /etc/profile
```
<br>add
```shell
export HADOOP_HOME=~/hadoop-2.7.3  # put Hadoop install path here
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
<br> then
```shell
sudo source /etc/profile
```
**6. Create a nickname for the instance**
```shell
sudo vim /etc/hosts
```
<br> add one line at the top, from here, the ip addresses in the following configurations are replaced with <font color="#dd0000">node1</font>
```she
192.168.18.128  node1  # replace the ip address here
```

**7.Modify hdfs-site.xml**
<br>```vim ~/hadoop-2.7.3/etc/hadoop/hdfs-site.xml```
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




