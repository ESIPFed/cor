# COR setup on new AWS AMI machine - April 2018

Ticket: https://github.com/ESIPFed/cor/issues/35

2018-04-19

## Initial state

Account `cor-admin1` with all sudo privilieges.

## Space

    [cor-admin1@ip-172-30-0-37 ~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  1.2G  6.6G  15% /


## Installations

### Docker

Ref: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html

    [cor-admin1@ip-172-30-0-37 ~]$ sudo yum update -y
    [sudo] password for cor-admin1:
    Loaded plugins: priorities, update-motd, upgrade-helper
    amzn-main                                                          | 2.1 kB  00:00:00
    amzn-updates                                                       | 2.5 kB  00:00:00
    No packages marked for update


    [cor-admin1@ip-172-30-0-37 ~]$ sudo yum install -y docker
    Loaded plugins: priorities, update-motd, upgrade-helper
    Resolving Dependencies
    --> Running transaction check
    ---> Package docker.x86_64 0:17.12.1ce-1.135.amzn1 will be installed
    --> Processing Dependency: xfsprogs for package: docker-17.12.1ce-1.135.amzn1.x86_64
    --> Processing Dependency: libseccomp.so.2()(64bit) for package: docker-17.12.1ce-1.135.amzn1.x86_64
    --> Processing Dependency: libltdl.so.7()(64bit) for package: docker-17.12.1ce-1.135.amzn1.x86_64
    --> Running transaction check
    ---> Package libseccomp.x86_64 0:2.3.1-2.4.amzn1 will be installed
    ---> Package libtool-ltdl.x86_64 0:2.4.2-20.4.8.5.32.amzn1 will be installed
    ---> Package xfsprogs.x86_64 0:4.5.0-9.21.amzn1 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ==========================================================================================
     Package            Arch         Version                         Repository          Size
    ==========================================================================================
    Installing:
     docker             x86_64       17.12.1ce-1.135.amzn1           amzn-updates        34 M
    Installing for dependencies:
     libseccomp         x86_64       2.3.1-2.4.amzn1                 amzn-main           79 k
     libtool-ltdl       x86_64       2.4.2-20.4.8.5.32.amzn1         amzn-main           51 k
     xfsprogs           x86_64       4.5.0-9.21.amzn1                amzn-main          1.7 M

    Transaction Summary
    ==========================================================================================
    Install  1 Package (+3 Dependent packages)

    Total download size: 36 M
    Installed size: 114 M
    Downloading packages:
    (1/4): libseccomp-2.3.1-2.4.amzn1.x86_64.rpm                       |  79 kB  00:00:00
    (2/4): xfsprogs-4.5.0-9.21.amzn1.x86_64.rpm                        | 1.7 MB  00:00:00
    (3/4): libtool-ltdl-2.4.2-20.4.8.5.32.amzn1.x86_64.rpm             |  51 kB  00:00:00
    (4/4): docker-17.12.1ce-1.135.amzn1.x86_64.rpm                     |  34 MB  00:00:01
    ------------------------------------------------------------------------------------------
    Total                                                      23 MB/s |  36 MB  00:00:01
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : libseccomp-2.3.1-2.4.amzn1.x86_64                                      1/4
      Installing : libtool-ltdl-2.4.2-20.4.8.5.32.amzn1.x86_64                            2/4
      Installing : xfsprogs-4.5.0-9.21.amzn1.x86_64                                       3/4
      Installing : docker-17.12.1ce-1.135.amzn1.x86_64                                                  4/4
      Verifying  : xfsprogs-4.5.0-9.21.amzn1.x86_64                                                     1/4
      Verifying  : docker-17.12.1ce-1.135.amzn1.x86_64                                                  2/4
      Verifying  : libtool-ltdl-2.4.2-20.4.8.5.32.amzn1.x86_64                                          3/4
      Verifying  : libseccomp-2.3.1-2.4.amzn1.x86_64                                                    4/4

    Installed:
      docker.x86_64 0:17.12.1ce-1.135.amzn1

    Dependency Installed:
      libseccomp.x86_64 0:2.3.1-2.4.amzn1           libtool-ltdl.x86_64 0:2.4.2-20.4.8.5.32.amzn1
      xfsprogs.x86_64 0:4.5.0-9.21.amzn1

    Complete!

    [cor-admin1@ip-172-30-0-37 COR]$ chkconfig --list docker
    docker         	0:off	1:off	2:on	3:on	4:on	5:on	6:off


#### Start docker

    [cor-admin1@ip-172-30-0-37 ~]$ sudo service docker start
    Starting cgconfig service:                                 [  OK  ]
    Starting docker:	.


#### Add `cor-admin1` to the docker group

    [cor-admin1@ip-172-30-0-37 ~]$ sudo usermod -a -G docker cor-admin1


#### Log out/back and verify

    [cor-admin1@ip-172-30-0-37 ~]$ docker info
    Containers: 0
     Running: 0
     Paused: 0
     Stopped: 0
    Images: 0
    Server Version: 17.12.1-ce
    Storage Driver: overlay2
     Backing Filesystem: extfs
     Supports d_type: true
     Native Overlay Diff: true
    Logging Driver: json-file
    Cgroup Driver: cgroupfs
    Plugins:
     Volume: local
     Network: bridge host macvlan null overlay
     Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
    Swarm: inactive
    Runtimes: runc
    Default Runtime: runc
    Init Binary: docker-init
    containerd version: 9b55aab90508bd389d7654c4baf173a981477d55
    runc version: 9f9c96235cc97674e935002fc3d78361b696a69e
    init version: 949e6fa
    Security Options:
     seccomp
      Profile: default
    Kernel Version: 4.9.81-35.56.amzn1.x86_64
    Operating System: Amazon Linux AMI 2017.09
    OSType: linux
    Architecture: x86_64
    CPUs: 2
    Total Memory: 3.86GiB
    Name: ip-172-30-0-37
    ID: GLQY:WK3E:OJYZ:XADO:XMDJ:ELS6:AGTG:DEGH:CQRD:E5LD:VHZM:2FGC
    Docker Root Dir: /var/lib/docker
    Debug Mode (client): false
    Debug Mode (server): false
    Registry: https://index.docker.io/v1/
    Labels:
    Experimental: false
    Insecure Registries:
     127.0.0.0/8
    Live Restore Enabled: false

#### Check space

    [cor-admin1@ip-172-30-0-37 ~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  1.3G  6.4G  17% /


### Docker Compose

Ref: https://github.com/docker/compose/releases

    [cor-admin1@ip-172-30-0-37 ~]$ sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    [sudo] password for cor-admin1:
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   617    0   617    0     0   2078      0 --:--:-- --:--:-- --:--:--  2084
    100 10.3M  100 10.3M    0     0  5055k      0  0:00:02  0:00:02 --:--:-- 9693k
    [cor-admin1@ip-172-30-0-37 ~]$ sudo chmod +x /usr/local/bin/docker-compose
    [cor-admin1@ip-172-30-0-37 ~]$ docker-compose --version
    docker-compose version 1.21.0, build 5920eb0

#### Check space

    [cor-admin1@ip-172-30-0-37 ~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  1.3G  6.4G  17% /


### Apache

Ref: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html

(but only focusing on Apache)

    [cor-admin1@ip-172-30-0-37 COR]$ sudo yum install -y httpd24
    [sudo] password for cor-admin1:
    Loaded plugins: priorities, update-motd, upgrade-helper
    amzn-main                                                            | 2.1 kB  00:00:00
    amzn-updates                                                         | 2.5 kB  00:00:00
    Resolving Dependencies
    --> Running transaction check
    ---> Package httpd24.x86_64 0:2.4.27-3.75.amzn1 will be installed
    --> Processing Dependency: httpd24-tools = 2.4.27-3.75.amzn1 for package: httpd24-2.4.27-3.75.amzn1.x86_64
    --> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd24-2.4.27-3.75.amzn1.x86_64
    --> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd24-2.4.27-3.75.amzn1.x86_64
    --> Running transaction check
    ---> Package apr.x86_64 0:1.5.2-5.13.amzn1 will be installed
    ---> Package apr-util.x86_64 0:1.5.4-6.18.amzn1 will be installed
    ---> Package httpd24-tools.x86_64 0:2.4.27-3.75.amzn1 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ============================================================================================
     Package               Arch           Version                    Repository            Size
    ============================================================================================
    Installing:
     httpd24               x86_64         2.4.27-3.75.amzn1          amzn-main            1.5 M
    Installing for dependencies:
     apr                   x86_64         1.5.2-5.13.amzn1           amzn-updates         118 k
     apr-util              x86_64         1.5.4-6.18.amzn1           amzn-updates          99 k
     httpd24-tools         x86_64         2.4.27-3.75.amzn1          amzn-main             96 k

    Transaction Summary
    ============================================================================================
    Install  1 Package (+3 Dependent packages)

    Total download size: 1.8 M
    Installed size: 4.6 M
    Downloading packages:
    (1/4): apr-util-1.5.4-6.18.amzn1.x86_64.rpm                          |  99 kB  00:00:00
    (2/4): httpd24-tools-2.4.27-3.75.amzn1.x86_64.rpm                    |  96 kB  00:00:00
    (3/4): apr-1.5.2-5.13.amzn1.x86_64.rpm                               | 118 kB  00:00:00
    (4/4): httpd24-2.4.27-3.75.amzn1.x86_64.rpm                          | 1.5 MB  00:00:00
    --------------------------------------------------------------------------------------------
    Total                                                       4.6 MB/s | 1.8 MB  00:00:00
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : apr-1.5.2-5.13.amzn1.x86_64                                              1/4
      Installing : apr-util-1.5.4-6.18.amzn1.x86_64                                         2/4
      Installing : httpd24-tools-2.4.27-3.75.amzn1.x86_64                                   3/4
      Installing : httpd24-2.4.27-3.75.amzn1.x86_64                                         4/4
      Verifying  : apr-1.5.2-5.13.amzn1.x86_64                                              1/4
      Verifying  : apr-util-1.5.4-6.18.amzn1.x86_64                                         2/4
      Verifying  : httpd24-2.4.27-3.75.amzn1.x86_64                                         3/4
      Verifying  : httpd24-tools-2.4.27-3.75.amzn1.x86_64                                   4/4

    Installed:
      httpd24.x86_64 0:2.4.27-3.75.amzn1

    Dependency Installed:
      apr.x86_64 0:1.5.2-5.13.amzn1                   apr-util.x86_64 0:1.5.4-6.18.amzn1
      httpd24-tools.x86_64 0:2.4.27-3.75.amzn1

    Complete!

    [cor-admin1@ip-172-30-0-37 COR]$ sudo service httpd start
    Starting httpd:                                            [  OK  ]
    [cor-admin1@ip-172-30-0-37 COR]$ sudo chkconfig httpd on
    [cor-admin1@ip-172-30-0-37 COR]$ chkconfig --list httpd
    httpd          	0:off	1:off	2:on	3:on	4:on	5:on	6:off



### w3m

Just to quickly check the local dispatch of Apache:

    [cor-admin1@ip-172-30-0-37 ~]$ sudo yum install -y w3m
    ...
    Complete!


### Git

    [cor-admin1@ip-172-30-0-37 ~]$ sudo yum install -y git
    ...
    [cor-admin1@ip-172-30-0-37 ~]$ git --version
    git version 2.13.6


### ORR

Refs:
- https://github.com/mmisw/orr-instance-template
- https://mmisw.org/orrdoc/install/

#### Clone template

    [cor-admin1@ip-172-30-0-37 ~]$ git clone https://github.com/mmisw/orr-instance-template.git COR
    Cloning into 'COR'...
    remote: Counting objects: 27, done.
    remote: Total 27 (delta 0), reused 0 (delta 0), pack-reused 27
    Unpacking objects: 100% (27/27), done.
    [cor-admin1@ip-172-30-0-37 ~]$ cd COR/
    [cor-admin1@ip-172-30-0-37 COR]$ rm -rf .git



#### Edit files

I basically reflected the setting as used in the previous machine.

#### Launch

    [cor-admin1@ip-172-30-0-37 COR]$ docker-compose up -d
    Creating network "cor_default" with the default driver
    Pulling agraph (franzinc/agraph:v6.1.1)...
    v6.1.1: Pulling from franzinc/agraph
    a3ed95caeb02: Pull complete
    204c9415d16d: Pull complete
    15902c04210d: Pull complete
    7f336448cc07: Pull complete
    3aa9ab796a54: Pull complete
    648ffa1703df: Pull complete
    Digest: sha256:59b47aea1a8cc36fabe1b2cd4f8786d02b6f1830204f3233f8b7207a22626f14
    Status: Downloaded newer image for franzinc/agraph:v6.1.1
    Pulling mongo (mongo:3.4.2)...
    3.4.2: Pulling from library/mongo
    e45e882ed798: Pull complete
    b03f96593290: Pull complete
    90df9ef9b571: Pull complete
    a647e09745f6: Pull complete
    b394c03fdf0b: Pull complete
    081d72a1938b: Pull complete
    fee529278709: Pull complete
    29c9f4827f3d: Pull complete
    56ce0b7433f1: Pull complete
    8a23c7b7039e: Pull complete
    7405cad42469: Pull complete
    Digest: sha256:8fa9ae74c30020bfb193e9abb327e92c3920af70e72a114da4373c4cd868fbd5
    Status: Downloaded newer image for mongo:3.4.2
    Pulling orr (mmisw/orr:3.7.0)...
    3.7.0: Pulling from mmisw/orr
    49521340b745: Pull complete
    e1eb4c8cdbe8: Pull complete
    eca9594bf267: Pull complete
    03384474141d: Pull complete
    88fdc72d9fa4: Pull complete
    3e4ce2bff25d: Pull complete
    51ddc72b84de: Pull complete
    64c86320c4e4: Pull complete
    8616c96f85aa: Pull complete
    8a37b5382220: Pull complete
    15c1bdf5e3a2: Pull complete
    8343486471ae: Pull complete
    519d575a2330: Pull complete
    04c6595ea8f0: Pull complete
    b93a9469f19f: Pull complete
    Digest: sha256:fc22495c5dcd18030a58743d096a94edf3376411609dd480e622ce830503d496
    Status: Downloaded newer image for mmisw/orr:3.7.0
    Creating mongo  ... done
    Creating agraph ... done
    Creating orr    ... done

    [cor-admin1@ip-172-30-0-37 COR]$ docker ps
    CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                      NAMES
    b54283d2b9be        mmisw/orr:3.7.0          "catalina.sh run"        28 seconds ago      Up 27 seconds       0.0.0.0:9090->8080/tcp     orr
    257fc8b79610        mongo:3.4.2              "docker-entrypoint.s…"   41 seconds ago      Up 38 seconds       0.0.0.0:27017->27017/tcp   mongo
    2c881f84de7d        franzinc/agraph:v6.1.1   "/bin/sh -c '/app/ag…"   41 seconds ago      Up 28 seconds       0.0.0.0:10035->10035/tcp   agraph


#### Basic check

    [cor-admin1@ip-172-30-0-37 COR]$ docker logs -f orr

Looks completely normal (including initialization of triple store).

#### Check space

Only 1G left!

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  6.7G 1015M  88% /

----

2018-04-20

## Extra space

Annie updated the storage to 20GB.

However, still seeing:

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  6.7G 1007M  88% /

Following https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html

    [cor-admin1@ip-172-30-0-37 COR]$ lsblk
    NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    xvda    202:0    0  20G  0 disk
    └─xvda1 202:1    0   8G  0 part /

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  6.7G 1006M  88% /

    [cor-admin1@ip-172-30-0-37 COR]$ sudo growpart /dev/xvda 1
    CHANGED: disk=/dev/xvda partition=1: start=4096 old: size=16773086,end=16777182 new: size=41938910,end=41943006

    [cor-admin1@ip-172-30-0-37 COR]$ lsblk
    NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    xvda    202:0    0  20G  0 disk
    └─xvda1 202:1    0  20G  0 part /

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1      7.8G  6.7G 1006M  88% /

    [cor-admin1@ip-172-30-0-37 COR]$ sudo resize2fs /dev/xvda1
    resize2fs 1.42.12 (29-Aug-2014)
    Filesystem at /dev/xvda1 is mounted on /; on-line resizing required
    old_desc_blocks = 1, new_desc_blocks = 2
    The filesystem on /dev/xvda1 is now 5242363 (4k) blocks long.

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1       20G  6.7G   13G  35% /

So, now we have 13G available.


## Data transfer

On previous/current machine:

- noted no user activity by looking at google analytics real-time
  panel and also a the ORR log.

- stopped the whole ORR system, in particular including Mongo

        $ docker-compose down

- created data tarball:

        $ sudo tar cf COR_data_2018-04-20.tgz orr_data mongo_data

- re-started ORR system

        $ docker-compose up -d

- transferred tarball to new machine:

        $ scp -i ESIPCOR.pem orr-cor/COR_data_2018-04-20.tgz cor-admin1@ec2-34-216-150-176.us-west-2.compute.amazonaws.com:

On new machine:

- stopped ORR system:

        [cor-admin1@ip-172-30-0-37 ~]$ cd COR
        [cor-admin1@ip-172-30-0-37 COR]$ source setenv.sh
        [cor-admin1@ip-172-30-0-37 COR]$ docker-compose down

- extracted data tarball:

        [cor-admin1@ip-172-30-0-37 COR]$ sudo tar xf ../COR_data_2018-04-20.tgz

- edited `config/orront.conf` to temporarily use
  `http://34.216.150.176/` as base location for the relevant links

- re-started ORR system

        $ docker-compose up -d

- from previous machine, also transferred the following:
    - `/etc/httpd/conf.d/orr.conf`
    - `/etc/httpd/conf.d/sweetontology.conf`
    - `/var/www/html/*`

- re-started apache:

        sudo service httpd graceful

- opened in my browser:
    - http://34.216.150.176/
    - http://34.216.150.176/ont/
  All OK as expected.


Check space:

    [cor-admin1@ip-172-30-0-37 COR]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        2.0G   56K  2.0G   1% /dev
    tmpfs           2.0G     0  2.0G   0% /dev/shm
    /dev/xvda1       20G  9.3G   11G  48% /

So, 11G still available after whole data transfer.

# NEXT: Actual final data transfer and cut-over

- determine date for cutover
- put note on current instance announcing downtime
- when the time comes, do the data transfer sequence above
- adjust `config/orront.conf` to revert the temporary changes
- launch system: `docker-compose up -d`
- Done!


# 2018-04-24

Put notice on both http://cor.esipfed.org/  and  http://cor.esipfed.org/ont :

> Please note: The ESIP COR site will soon be transitioning to a new server with final cutover planned for next Friday, April 27, 2018.
> Please consider the site in read-only mode until this process is completed. (This notice will not be displayed as an indication of such completion.)
> We apologize for any inconvenience.

The above done as follows:

For http://cor.esipfed.org/ :

    vim /var/www/html/index.html # to insert snippet right after `<body>`

For http://cor.esipfed.org/ont/ :

    ssh -i ~/.ssh/cor_carueda_rsa carueda@54.183.173.69
    cd /tmp
    docker cp orr:/usr/local/tomcat/webapps/ont/index.html .
    vim index.html  # to insert the snippet
    docker cp index.html orr:/usr/local/tomcat/webapps/ont/

# 2018-04-27

## Final data transfer

On previous/current machine:

- stopped the whole ORR system

        cd orr-cor/
        docker-compose down

- created data tarball:

        sudo tar cf COR_data_2018-04-27.tgz orr_data mongo_data

- re-started ORR system, and restored index.html with cutoever notice:

        docker-compose up -d
        docker cp index.html orr:/usr/local/tomcat/webapps/ont/

- transferred tarball to new machine:

        scp -i ../ESIPCOR.pem COR_data_2018-04-27.tgz cor-admin1@ec2-34-216-150-176.us-west-2.compute.amazonaws.com:

On new machine:

- stopped ORR system:

        [cor-admin1@ip-172-30-0-37 ~]$ cd COR
        [cor-admin1@ip-172-30-0-37 COR]$ source setenv.sh
        [cor-admin1@ip-172-30-0-37 COR]$ docker-compose down

- extracted data tarball:

        [cor-admin1@ip-172-30-0-37 COR]$ sudo tar xf ../COR_data_2018-04-27.tgz

- edited `config/orront.conf` to REVERT the temporary use of
  `http://34.216.150.176/` as base location for the relevant links,
  so now using the actual base `http://cor.esipfed.org/`:

        [cor-admin1@ip-172-30-0-37 COR]$ vim config/orront.conf

- re-started ORR system

        [cor-admin1@ip-172-30-0-37 COR]$ docker-compose up -d
        Creating network "cor_default" with the default driver
        Creating mongo  ... done
        Creating agraph ... done
        Creating orr    ... done

- opened in my browser to check basic dispatch OK:
    - http://34.216.150.176/
    - http://34.216.150.176/ont/

- Re-populated triple store:

    From my local machine, and using [HTTPie](https://httpie.org/):

        http -pb -a carueda:MYPWHERE put http://34.216.150.176/ont/api/v0/ts/
        {
            "msg": "Done, 254 ontologies reloaded"
        }

    (see [triple store reload operation](http://cor.esipfed.org/ontapi/#!/triplestore/reloadOntsInTriplestore).)

    **NOTE**: Not using the Admin option in the GUI itself because the
    configuration entry for the API endpoint is now back to
    `http://cor.esipfed.org` BUT the IP cutover is not yet in place at
    this right moment.

- Check space:

        [cor-admin1@ip-172-30-0-37 COR]$ df -h
        Filesystem      Size  Used Avail Use% Mounted on
        devtmpfs        2.0G   56K  2.0G   1% /dev
        tmpfs           2.0G     0  2.0G   0% /dev/shm
        /dev/xvda1       20G   12G  8.3G  58% /

    So, with the triple store also populated, we actually now have
    8.3G space available.

- Sent email to Annie to proceed with IP cutover.

# 2018-07-20 - sweetontology.net

Just noted that the cronjob on the previous machine to perform automatic registration of SWEET ontology updates into COR has not been enabled on the new machine!

Following are steps to do that.

    [cor-admin1@ip-172-30-0-37 ~]$ git clone https://github.com/ESIPFed/sweet-tools.git
    ...

    [cor-admin1@ip-172-30-0-37 ~]$ cd sweet-tools/sc/watchdog/


Install [Ammonite](http://ammonite.io/#ScalaScripts), which requires
Java8:

    [cor-admin1@ip-172-30-0-37 watchdog]$ sudo yum install java-1.8.0-openjdk
    ...

    [cor-admin1@ip-172-30-0-37 watchdog]$ sudo alternatives --config java

    There are 2 programs which provide 'java'.

      Selection    Command
    -----------------------------------------------
    *+ 1           /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/bin/java
       2           /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/java

    Enter to keep the current selection[+], or type selection number: 2
    
    [cor-admin1@ip-172-30-0-37 watchdog]$ java -version
    openjdk version "1.8.0_171"
    OpenJDK Runtime Environment (build 1.8.0_171-b10)
    OpenJDK 64-Bit Server VM (build 25.171-b10, mixed mode)

    [cor-admin1@ip-172-30-0-37 watchdog]$ sudo sh -c '(echo "#!/usr/bin/env sh" && curl -L https://github.com/lihaoyi/Ammonite/releases/download/1.1.2/2.12-1.1.2) > /usr/local/bin/amm && chmod +x /usr/local/bin/amm'
    ...

As explained at https://github.com/ESIPFed/sweet-tools/tree/master/sc/watchdog
and according to previous configuration:

    [cor-admin1@ip-172-30-0-37 watchdog]$ cat > local.cor.conf
    endpoint   = "http://cor.esipfed.org/ont/api"
    userName   = "sweetbot"
    userPass   = "***"
    orgName    = "esip"

    visibility = "public"
    status     = "stable"

    [cor-admin1@ip-172-30-0-37 watchdog]$ ./watchdog.sc refreshCorInfo
    Getting COR info...
    	http://sweetontology.net/human
    	http://sweetontology.net/humanAgriculture
    	http://sweetontology.net/humanCommerce
    	http://sweetontology.net/humanDecision
    	http://sweetontology.net/humanEnvirAssessment
    	http://sweetontology.net/humanEnvirConservation
    	http://sweetontology.net/humanEnvirControl
    	http://sweetontology.net/humanEnvirStandards
    	http://sweetontology.net/humanJurisdiction
    	http://sweetontology.net/humanKnowledgeDomain
    	http://sweetontology.net/humanResearch
    	http://sweetontology.net/humanTechReadiness
    	http://sweetontology.net/humanTransportation
    	http://sweetontology.net/matr
    	http://sweetontology.net/matrAerosol
    	http://sweetontology.net/matrAnimal
    	http://sweetontology.net/matrBiomass
    	http://sweetontology.net/matrCompound
    	http://sweetontology.net/matrElement
    	http://sweetontology.net/matrElementalMolecule
    	http://sweetontology.net/matrEnergy
    	http://sweetontology.net/matrEquipment
    	http://sweetontology.net/matrFacility
    	http://sweetontology.net/matrIndustrial
    	http://sweetontology.net/matrInstrument
    	http://sweetontology.net/matrIon
    	http://sweetontology.net/matrIsotope
    	http://sweetontology.net/matrMicrobiota
    	http://sweetontology.net/matrMineral
    	http://sweetontology.net/matrNaturalResource
    	http://sweetontology.net/matrOrganicCompound
    	http://sweetontology.net/matrParticle
    	http://sweetontology.net/matrPlant
    	http://sweetontology.net/matrRock
    	http://sweetontology.net/matrRockIgneous
    	http://sweetontology.net/matrSediment
    	http://sweetontology.net/matrWater
    	http://sweetontology.net/phen
    	http://sweetontology.net/phenAtmo
    	http://sweetontology.net/phenAtmoCloud
    	http://sweetontology.net/phenAtmoFog
    	http://sweetontology.net/phenAtmoFront
    	http://sweetontology.net/phenAtmoLightning
    	http://sweetontology.net/phenAtmoPrecipitation
    	http://sweetontology.net/phenAtmoPressure
    	http://sweetontology.net/phenAtmoSky
    	http://sweetontology.net/phenAtmoTransport
    	http://sweetontology.net/phenAtmoWind
    	http://sweetontology.net/phenAtmoWindMesoscale
    	http://sweetontology.net/phenBiol
    	http://sweetontology.net/phenCryo
    	http://sweetontology.net/phenCycle
    	http://sweetontology.net/phenCycleMaterial
    	http://sweetontology.net/phenEcology
    	http://sweetontology.net/phenElecMag
    	http://sweetontology.net/phenEnergy
    	http://sweetontology.net/phenEnvirImpact
    	http://sweetontology.net/phenFluidDynamics
    	http://sweetontology.net/phenFluidInstability
    	http://sweetontology.net/phenFluidTransport
    	http://sweetontology.net/phenGeol
    	http://sweetontology.net/phenGeolFault
    	http://sweetontology.net/phenGeolGeomorphology
    	http://sweetontology.net/phenGeolSeismicity
    	http://sweetontology.net/phenGeolTectonic
    	http://sweetontology.net/phenGeolVolcano
    	http://sweetontology.net/phenHelio
    	http://sweetontology.net/phenHydro
    	http://sweetontology.net/phenMixing
    	http://sweetontology.net/phenOcean
    	http://sweetontology.net/phenOceanCoastal
    	http://sweetontology.net/phenOceanDynamics
    	http://sweetontology.net/phenPlanetClimate
    	http://sweetontology.net/phenReaction
    	http://sweetontology.net/phenSolid
    	http://sweetontology.net/phenStar
    	http://sweetontology.net/phenSystem
    	http://sweetontology.net/phenSystemComplexity
    	http://sweetontology.net/phenWave
    	http://sweetontology.net/phenWaveNoise
    	http://sweetontology.net/proc
    	http://sweetontology.net/procChemical
    	http://sweetontology.net/procPhysical
    	http://sweetontology.net/procStateChange
    	http://sweetontology.net/procWave
    	http://sweetontology.net/prop
    	http://sweetontology.net/propBinary
    	http://sweetontology.net/propCapacity
    	http://sweetontology.net/propCategorical
    	http://sweetontology.net/propCharge
    	http://sweetontology.net/propChemical
    	http://sweetontology.net/propConductivity
    	http://sweetontology.net/propCount
    	http://sweetontology.net/propDifference
    	http://sweetontology.net/propDiffusivity
    	http://sweetontology.net/propDimensionlessRatio
    	http://sweetontology.net/propEnergy
    	http://sweetontology.net/propEnergyFlux
    	http://sweetontology.net/propFraction
    	http://sweetontology.net/propFunction
    	http://sweetontology.net/propIndex
    	http://sweetontology.net/propMass
    	http://sweetontology.net/propMassFlux
    	http://sweetontology.net/propOrdinal
    	http://sweetontology.net/propPressure
    	http://sweetontology.net/propQuantity
    	http://sweetontology.net/propRotation
    	http://sweetontology.net/propSpace
    	http://sweetontology.net/propSpaceDirection
    	http://sweetontology.net/propSpaceDistance
    	http://sweetontology.net/propSpaceHeight
    	http://sweetontology.net/propSpaceLocation
    	http://sweetontology.net/propSpaceMultidimensional
    	http://sweetontology.net/propSpaceThickness
    	http://sweetontology.net/propSpeed
    	http://sweetontology.net/propTemperature
    	http://sweetontology.net/propTemperatureGradient
    	http://sweetontology.net/propTime
    	http://sweetontology.net/propTimeFrequency
    	http://sweetontology.net/realm
    	http://sweetontology.net/realmAstroBody
    	http://sweetontology.net/realmAstroHelio
    	http://sweetontology.net/realmAstroStar
    	http://sweetontology.net/realmAtmo
    	http://sweetontology.net/realmAtmoBoundaryLayer
    	http://sweetontology.net/realmAtmoWeather
    	http://sweetontology.net/realmBiolBiome
    	http://sweetontology.net/realmClimateZone
    	http://sweetontology.net/realmCryo
    	http://sweetontology.net/realmEarthReference
    	http://sweetontology.net/realmGeol
    	http://sweetontology.net/realmGeolBasin
    	http://sweetontology.net/realmGeolConstituent
    	http://sweetontology.net/realmGeolContinental
    	http://sweetontology.net/realmGeolOceanic
    	http://sweetontology.net/realmGeolOrogen
    	http://sweetontology.net/realmHydro
    	http://sweetontology.net/realmHydroBody
    	http://sweetontology.net/realmLandAeolian
    	http://sweetontology.net/realmLandCoastal
    	http://sweetontology.net/realmLandFluvial
    	http://sweetontology.net/realmLandGlacial
    	http://sweetontology.net/realmLandOrographic
    	http://sweetontology.net/realmLandProtected
    	http://sweetontology.net/realmLandTectonic
    	http://sweetontology.net/realmLandVolcanic
    	http://sweetontology.net/realmLandform
    	http://sweetontology.net/realmOcean
    	http://sweetontology.net/realmOceanFeature
    	http://sweetontology.net/realmOceanFloor
    	http://sweetontology.net/realmRegion
    	http://sweetontology.net/realmSoil
    	http://sweetontology.net/rela
    	http://sweetontology.net/relaChemical
    	http://sweetontology.net/relaClimate
    	http://sweetontology.net/relaHuman
    	http://sweetontology.net/relaMath
    	http://sweetontology.net/relaPhysical
    	http://sweetontology.net/relaProvenance
    	http://sweetontology.net/relaSci
    	http://sweetontology.net/relaSpace
    	http://sweetontology.net/relaTime
    	http://sweetontology.net/repr
    	http://sweetontology.net/reprDataFormat
    	http://sweetontology.net/reprDataModel
    	http://sweetontology.net/reprDataProduct
    	http://sweetontology.net/reprDataService
    	http://sweetontology.net/reprDataServiceAnalysis
    	http://sweetontology.net/reprDataServiceGeospatial
    	http://sweetontology.net/reprDataServiceReduction
    	http://sweetontology.net/reprDataServiceValidation
    	http://sweetontology.net/reprMath
    	http://sweetontology.net/reprMathFunction
    	http://sweetontology.net/reprMathFunctionOrthogonal
    	http://sweetontology.net/reprMathGraph
    	http://sweetontology.net/reprMathOperation
    	http://sweetontology.net/reprMathSolution
    	http://sweetontology.net/reprMathStatistics
    	http://sweetontology.net/reprSciComponent
    	http://sweetontology.net/reprSciFunction
    	http://sweetontology.net/reprSciLaw
    	http://sweetontology.net/reprSciMethodology
    	http://sweetontology.net/reprSciModel
    	http://sweetontology.net/reprSciProvenance
    	http://sweetontology.net/reprSciUnits
    	http://sweetontology.net/reprSpace
    	http://sweetontology.net/reprSpaceCoordinate
    	http://sweetontology.net/reprSpaceDirection
    	http://sweetontology.net/reprSpaceGeometry
    	http://sweetontology.net/reprSpaceGeometry3D
    	http://sweetontology.net/reprSpaceReferenceSystem
    	http://sweetontology.net/reprTime
    	http://sweetontology.net/reprTimeDay
    	http://sweetontology.net/reprTimeSeason
    	http://sweetontology.net/state
    	http://sweetontology.net/stateBiological
    	http://sweetontology.net/stateChemical
    	http://sweetontology.net/stateDataProcessing
    	http://sweetontology.net/stateEnergyFlux
    	http://sweetontology.net/stateFluid
    	http://sweetontology.net/stateOrdinal
    	http://sweetontology.net/statePhysical
    	http://sweetontology.net/stateRealm
    	http://sweetontology.net/stateRole
    	http://sweetontology.net/stateRoleBiological
    	http://sweetontology.net/stateRoleChemical
    	http://sweetontology.net/stateRoleGeographic
    	http://sweetontology.net/stateRoleImpact
    	http://sweetontology.net/stateRoleRepresentative
    	http://sweetontology.net/stateRoleTrust
    	http://sweetontology.net/stateSolid
    	http://sweetontology.net/stateSpace
    	http://sweetontology.net/stateSpaceConfiguration
    	http://sweetontology.net/stateSpaceScale
    	http://sweetontology.net/stateSpectralBand
    	http://sweetontology.net/stateSpectralLine
    	http://sweetontology.net/stateStorm
    	http://sweetontology.net/stateSystem
    	http://sweetontology.net/stateThermodynamic
    	http://sweetontology.net/stateTime
    	http://sweetontology.net/stateTimeCycle
    	http://sweetontology.net/stateTimeFrequency
    	http://sweetontology.net/stateTimeGeologic
    	http://sweetontology.net/stateVisibility
    	http://sweetontology.net/sweetAll

    [cor-admin1@ip-172-30-0-37 watchdog]$ ./watchdog.sc compare
    Getting Github info: .................................................................................................................................................................................................................................
    (225 paths retrieved)
    No changes.

All above as expected. Now, the cronjob:

    [cor-admin1@ip-172-30-0-37 watchdog]$ crontab -e
    
    MAILTO="..."

    PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/aws/bin:/home/cor-admin1/.local/bin:/home/cor-admin1/bin

    29 */6 * * *  cd /home/cor-admin1/sweet-tools/sc/watchdog/ && ./watchdog.cronjob.sh >> watchdog.cronjob.out


**Done.**


# 2019-03-21

Initial login promopted upgrade of various packages... this was initiated by Lewis McGibbney and completed successfully. 

## Issue with dispatch of sweetontology.net/
There is an issue serving the **RewriteRule    /(.+) http://cor.esipfed.org/ont/api/v0/ont?iri=http://sweetontology.net/$1 [P,QSA,NE,L]** where any resource requested via sweetontology.net/some/resource is not being correctly serviced by COR at http://cor.esipfed.org/ont/api/v0/ont?iri=http://sweetontology.net/some/resource.
