# Hadoop-setup
The project is goint to set up a small Hadoop cluster on AWS, including 1 namenode and 3 datanodes.

### Namenode set-up
**EC2 instance:** Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - t2.micro (free tier)<br>
**Security Group:** Add inbound rules: Allow all trffic, all protocol, all port range from My IP

**1.Update the instance:**
<br>```ubuntu@ip-10-0-1-161:~$ sudo apt-get update && sudo apt-get -y dist-upgrade ```
