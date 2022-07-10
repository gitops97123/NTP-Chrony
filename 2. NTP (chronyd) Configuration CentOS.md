#  NTP (chronyd) Configuration 

|Pre-requisites|Info|
|-------|-----------|
|domain:| lab.local |
|hostname| server-world.lab.local| 
|IP Addresss| 192.168.122.168|
|OS Release | Centos 7 Minimal| 

Install chrony and Configure NTP server for time adjustment.

    [root@server-world ~]# yum install chrony -y 
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    * base: repo.extreme-ix.org
    * extras: repo.extreme-ix.org
    * updates: repo.extreme-ix.org
    Resolving Dependencies
    --> Running transaction check
    ---> Package chrony.x86_64 0:3.4-1.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    =================================================================================================================
    Package                   Arch                      Version                       Repository               Size
    =================================================================================================================
    Installing:
    chrony                    x86_64                    3.4-1.el7                     base                    251 k

    Transaction Summary
    =================================================================================================================
    Install  1 Package

    Total download size: 251 k
    Installed size: 491 k
    Downloading packages:
    chrony-3.4-1.el7.x86_64.rpm                                                               | 251 kB  00:00:00     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : chrony-3.4-1.el7.x86_64                                                                       1/1 
    Verifying  : chrony-3.4-1.el7.x86_64                                                                       1/1 

    Installed:
    chrony.x86_64 0:3.4-1.el7                                                                                      

    Complete!

configure chrony. 

    [root@server-world ~]# vi /etc/chrony.conf

    # Use public servers from the pool.ntp.org project.
    # Please consider joining the pool (http://www.pool.ntp.org/join.html).
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst
    server ntp1.jst.mfeed.ad.jp iburst
    server ntp2.jst.mfeed.ad.jp iburst
    server ntp3.jst.mfeed.ad.jp iburst

    add line 28 to allow access 
    # Allow NTP client access from local network.
    allow 192.168.122.0/24

start and enable chronyd service 

    [root@server-world ~]# systemctl enable --now chronyd

check and verify the time synchronization 

    [root@server-world ~]# chronyc sources 
    210 Number of sources = 3
    MS Name/IP address         Stratum Poll Reach LastRx Last sample               
    ===============================================================================
    ^+ ntp1.jst.mfeed.ad.jp          2   6    17     2    -33us[+1016us] +/-  144ms
    ^* ntp2.jst.mfeed.ad.jp          2   6    17     1   -510us[ +540us] +/-  118ms
    ^+ ntp3.jst.mfeed.ad.jp          2   6    17     1    +29ms[  +30ms] +/-  178ms
    [root@server-world ~]# 
    [root@server-world ~]# 
    [root@server-world ~]# timedatectl 
        Local time: Sun 2022-07-10 19:44:58 IST
    Universal time: Sun 2022-07-10 14:14:58 UTC
            RTC time: Sun 2022-07-10 14:14:57
        Time zone: Asia/Kolkata (IST, +0530)
        NTP enabled: yes
    NTP synchronized: yes
    RTC in local TZ: no
        DST active: n/a

If Firewalld is running, allow NTP service. NTP uses 123/UDP.
 
    [root@server-world ~]# firewall-cmd --permanent --add-service=ntp
    success

    [root@server-world ~]# firewall-cmd --reload 
    success



