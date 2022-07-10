#  NTP (NTPd) Configuration 

|Pre-requisites|Info|
|-------|-----------|
|domain:| lab.local |
|hostname| server-world.lab.local| 
|IP Addresss| 192.168.122.168|
|OS Release | Centos 7 Minimal| 

Install NTPd and Configure NTP server for time adjustment.

    [root@server-world ~]# yum update -y && reboot 

    [root@server-world ~]# cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6


    [root@server-world ~]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 52:54:00:eb:c3:20 brd ff:ff:ff:ff:ff:ff
        inet 192.168.122.168/24 brd 192.168.122.255 scope global noprefixroute dynamic eth0
        valid_lft 2961sec preferred_lft 2961sec
        inet6 fe80::45bc:3022:2c9b:c1c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever

Add host Entry in /etc/hosts. 

    [root@server-world ~]# cat /etc/hosts 
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    192.168.122.168  server-world.lab.local server

Install the require package for ntp server. 

    [root@server-world ~]# yum -y install ntp
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    * base: repo.extreme-ix.org
    * extras: repo.extreme-ix.org
    * updates: repo.extreme-ix.org
    Package yum-3.4.3-168.el7.centos.noarch already installed and latest version
    No package install available.
    Resolving Dependencies
    --> Running transaction check
    ---> Package ntp.x86_64 0:4.2.6p5-29.el7.centos.2 will be installed
    --> Processing Dependency: ntpdate = 4.2.6p5-29.el7.centos.2 for package: ntp-4.2.6p5-29.el7.centos.2.x86_64
    --> Processing Dependency: libopts.so.25()(64bit) for package: ntp-4.2.6p5-29.el7.centos.2.x86_64
    --> Running transaction check
    ---> Package autogen-libopts.x86_64 0:5.18-5.el7 will be installed
    ---> Package ntpdate.x86_64 0:4.2.6p5-29.el7.centos.2 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    =================================================================================================================
    Package                      Arch                Version                                Repository         Size
    =================================================================================================================
    Installing:
    ntp                          x86_64              4.2.6p5-29.el7.centos.2                base              549 k
    Installing for dependencies:
    autogen-libopts              x86_64              5.18-5.el7                             base               66 k
    ntpdate                      x86_64              4.2.6p5-29.el7.centos.2                base               87 k

    Transaction Summary
    =================================================================================================================
    Install  1 Package (+2 Dependent packages)

    Total download size: 701 k
    Installed size: 1.6 M
    Downloading packages:
    (1/3): autogen-libopts-5.18-5.el7.x86_64.rpm                                              |  66 kB  00:00:00     
    (2/3): ntp-4.2.6p5-29.el7.centos.2.x86_64.rpm                                             | 549 kB  00:00:00     
    (3/3): ntpdate-4.2.6p5-29.el7.centos.2.x86_64.rpm                                         |  87 kB  00:00:00     
    -----------------------------------------------------------------------------------------------------------------
    Total                                                                            1.7 MB/s | 701 kB  00:00:00     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : autogen-libopts-5.18-5.el7.x86_64                                                             1/3 
    Installing : ntpdate-4.2.6p5-29.el7.centos.2.x86_64                                                        2/3 
    Installing : ntp-4.2.6p5-29.el7.centos.2.x86_64                                                            3/3 
    Verifying  : ntpdate-4.2.6p5-29.el7.centos.2.x86_64                                                        1/3 
    Verifying  : ntp-4.2.6p5-29.el7.centos.2.x86_64                                                            2/3 
    Verifying  : autogen-libopts-5.18-5.el7.x86_64                                                             3/3 

    Installed:
    ntp.x86_64 0:4.2.6p5-29.el7.centos.2                                                                           

    Dependency Installed:
    autogen-libopts.x86_64 0:5.18-5.el7                  ntpdate.x86_64 0:4.2.6p5-29.el7.centos.2                 

    Complete!

Configure ntp 

    [root@server-world ~]# vi /etc/ntp.conf 

    # line 18: add the network range you allow to receive requests
    restrict 192.168.122.0 mask 255.255.255.0 nomodify notrap

    # comment the all pool servers
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst
    
    # add new servers for synchronization

    server ntp1.jst.mfeed.ad.jp iburst
    server ntp2.jst.mfeed.ad.jp iburst
    server ntp3.jst.mfeed.ad.jp iburst

    [root@server-world ~]# systemctl start ntpd
    [root@server-world ~]# systemctl enable ntpd 
    Created symlink from /etc/systemd/system/multi-user.target.wants/ntpd.service to /usr/lib/systemd/system/ntpd.service.

If Firewalld is running, allow NTP service. NTP uses 123/UDP.

    [root@server-world ~]# firewall-cmd --add-service=ntp --permanent
    success
    [root@server-world ~]# firewall-cmd --reload
    success

Check and Verify the time synchronization.

    [root@server-world ~]# ntpq -p
        remote           refid      st t when poll reach   delay   offset  jitter
    ==============================================================================
    *time.cloudflare 10.27.8.117      3 u   11   64    7   61.849   -3.356   2.320
    kyc2docs.x10cor 64.227.167.110   3 u   11   64    7   34.407   -0.298   2.522
    static.226.144. 194.58.200.20    2 u   11   64    7  183.079    3.169   4.952
    ntp1.jst.mfeed. 133.243.236.17   2 u    7   64    5  144.273   -8.786   2.337
    ntp2.jst.mfeed. 133.243.236.18   2 u    9   64    5  147.555   -6.212   1.760
    ntp3.jst.mfeed. 133.243.236.19   2 u    9   64    7  152.075  -10.599   2.973

    [root@server-world ~]# 

