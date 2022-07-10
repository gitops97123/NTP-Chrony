# Configure NTP Client : CentOS
The settings of NTP Client on CentOS are mostly the same with Server's settings, so refer to NTPd Settings or Chrony Settings. For different settings from Server's one, Clients don't need to receive time synchronization requests from other computers, so it does not need to set access permission.

|Pre-requisites|Info|
|-------|-----------|
|domain:| lab.local |
|hostname| servera.lab.local| 
|IP Addresss| 192.168.122.44|
|OS Release | Centos 7 Minimal| 


If you don't use NTP service daemon but use a command to sync time at once, use ntpdate like follows

    [root@servera yum.repos.d]# yum -y install ntpdate
    ...
    Resolving Dependencies
    --> Running transaction check
    ...
    --> Finished Dependency Resolution
    ...
    Installed:
    ntpdate.x86_64 0:4.2.6p5-29.el7.centos. 
                                                                      
    Complete!

    [root@servera ~]# ntpdate ntp1.jst.mfeed.ad.jp
    10 Jul 19:57:49 ntpdate[1323]: adjust time server 210.173.160.27 offset -0.006994 sec
    
    [root@servera ~]# systemctl enable ntpdate
    Created symlink from /etc/systemd/system/multi-user.target.wants/ntpdate.service to /usr/lib/systemd/system/ntpdate.service.
    
    [root@servera ~]# 