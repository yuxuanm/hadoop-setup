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

**5.Modify core_site.xml**
<br>```sodu vim ~/hadoop-2.7.3/etc/hadoop/core_site.xml```
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value><nnode>:9000</value>
  </property>
</configuration>
  ```
**6.Modify hdfs-site/xml**
<br>add configuration
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

**7. Modify yarn-site.xml**
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

**8. Setup slaves**
```shell
vim ~/hadoop-2.7.3/etc/hadoop/slaves
```




