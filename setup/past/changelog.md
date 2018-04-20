(file copied from previous private git repo)

### 2018-02-14

- Migrated the whole cor.esipfed.org site back to the Amazon AWS instance
  that was used until a year ago.
- The COR migration basically consisted of:
  - On ECITE machine:
    - docker-compose down
    - make tarball of the base directory `orr-cor` and transfer it to Amazon machine
    - docker-compose up -d   # just to continue running on current location
  - On Amazon machine:
    - docker-compose down    # the old ORR 3.2.1 version was still running
    - rename `orr-cor` directory
    - expand tarball from ECITE
    - review and edit docker-compose.yml
    - docker-compose up -d
    - reviewed and adjusted Apache config for cor.esipfed.org
    - That's basically it.

- Besides the COR itself, also did the following:
  - Apache configuration for sweetontology.net
  - enabled SWEET watchdog script (incl installation of openjdk 1.8.0)
- Removed a bunch of old stuff (on the Amazon machine) related with the
  old version 2 of the ORR system, including old AllegroGraph and
  /opt/MMI-ORR/.

- Also, per warning at login, ran `sudo yum update`, which among many
  things also brought an updated docker engine (17.09.1-ce).
  Then, re-launched the ORR.  All looks good.

### 2018-01-10

- As some point during Felimon's presentation of the COR at the ESIP semantic symposium
  he noticed the COR stopped responding to requests. Upon seeing out-of-memory errors
  in the logs (docker logs -f orr), Carlos went ahead and restarted the orr container.
  COR continued to function normally. Presumably, the extra overhead from multiple
  users triggered the problem. Worth noting, no tuning at all has yet been done to
  give the containers appropriate resources. This is a TODO!
  For now, I just installed [`ctop`](https://github.com/bcicen/ctop) and took a look
  at the output at this point, see attached screenshots under `misc/`.
  As already said, further investigation is needed but right now we can see that agraph
  seems to easily get to the limit of the allocated memory.
  The other screenshot shows the result right after a `docker restart agraph`.

### 2017-11-23

- upgrade to mmisw/orr:3.7.0 - simplified configurability

### 2017-11-03

- upgrade ORR to 3.6.8

### 2017-10-12

- upgrade ORR to 3.6.7

### 2017-10-04

- upgrade ORR to 3.6.6

### 2017-09-20

- upgrade ORR to 3.6.5

- upgrade Docker (to CE 17.06.2) -- see readme-ecite.md

- After a reboot I just noted that Apache was not enabled to start at boot time!

        [centos@carlos0213 orr]$ sudo systemctl enable httpd
        Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.

### 2017-09-15

- set AG port mapping only to "10035:10035", as 10035 is the only port that need to be exposed

### 2017-08-01

- `sudo ln /etc/httpd/conf.d/orr.conf ./conf.d__orr.conf` and add it for version control
- `sudo /usr/sbin/setsebool -P httpd_can_network_connect 1` needed upon apparently
  a recent OS update that put in place a more restricted linux env.

### 2017-05-22:

- enable CORS from the SPARQL endpoint: `Header set Access-Control-Allow-Origin "*"` (/etc/httpd/conf.d/orr.conf)

### 2017-03-07:

- upgrade to mmisw/orr:3.4.1
- adjust crontab to: `@reboot sleep 60s && docker start mongo agraph orr`


### 2017-02-17/18/19

- site migration from previous amazon machine to ECITE (see readme-ecite.md)
- upgrade to mmisw/orr-ont:3.2.2

### 2016-09-01

- set cronjob to launch system at reboot.

### 2016-08-31

- initial complete set-up based on docker-compose

### 2016-08-27

Installed docker-compose from https://github.com/docker/compose/releases:

    $ curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /tmp/docker-compose
	$ chmod +x /tmp/docker-compose
	$ sudo mv /tmp/docker-compose /usr/local/bin/
	$ docker-compose --version
    docker-compose version 1.8.0, build f3628c7


### 2016-04-01

Installed Docker according to [this Amazon guide]( http://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#install_docker):

    sudo yum update -y
    sudo yum install -y docker
    sudo service docker start
    sudo usermod -a -G docker carueda


### Earlier?

...
