# Hadoop-setup
The project is goint to set up a small Hadoop cluster on AWS, including 1 namenode and 3 datanodes.

### Namenode set-up
**EC2 instance:** Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - t2.micro (free tier)<br>
**Security Group:** Add inbound rules: Allow all trffic, all protocol, all port range from My IP

### All Nodes:
**1.Update the instance:**
<br>```ubuntu@ip-10-0-1-161:~$ sudo apt-get update && sudo apt-get -y dist-upgrade ```

**2.Install Java on all instances:**
<br>```sudo apt-get -y install openjdk-8-jdk-headless```

**3.Install Hadoop(2.7.3) on all instances**
<br>```wget http://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz```
<br>```tar xvzf hadoop-2.7.3.tar.gz```

**4.Set up Java_Home**
```sudo vim ~/hadoop-2.7.3/etc/hadoop/hadoop-env.sh```
<br>export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

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
