## Setup of the ESIP COR instance on ECITE

### 2017-09-20

Upon noticing rather frequent "device busy" errors when trying to stop/rm containers,
decided to go ahead with an upgrade of Docker altogether. 

Just followed the instructions at https://docs.docker.com/engine/installation/linux/docker-ce/centos/

    $ sudo yum remove docker \
                      docker-common \
                      docker-selinux \
                      docker-engine 


    $ sudo yum install -y yum-utils \
      device-mapper-persistent-data \
      lvm2

    $ sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
        
        
    $ yum list docker-ce.x86_64  --showduplicates | sort -r
     * updates: mirror.solarvps.com
    Loaded plugins: fastestmirror
     * extras: centosc6.centos.org
    docker-ce.x86_64            17.06.2.ce-1.el7.centos             docker-ce-stable
    docker-ce.x86_64            17.06.1.ce-1.el7.centos             docker-ce-stable
    docker-ce.x86_64            17.06.0.ce-1.el7.centos             docker-ce-stable
    docker-ce.x86_64            17.03.2.ce-1.el7.centos             docker-ce-stable
    docker-ce.x86_64            17.03.1.ce-1.el7.centos             docker-ce-stable
    docker-ce.x86_64            17.03.0.ce-1.el7.centos             docker-ce-stable    
    
    
    $ sudo yum install docker-ce-17.06.2.ce-1.el7.centos

    $ sudo systemctl start docker

    $ sudo systemctl enable docker
    
    $ docker --version
    Docker version 17.06.2-ce, build cec0b72
    
    

### 2017-02-14

- installed Docker, Docker Compose, Apache  (and other utilities: lynx, vim, git)
- successfully ran ORR
- configure apache proxy-passes to expose /ont and /sparql
- all of this using 199.26.254.133 as base server

In some more detail:

Installed Docker according to:

- https://docs.docker.com/engine/installation/linux/centos/
- https://docs.docker.com/engine/installation/linux/linux-postinstall/

    $ sudo yum install -y yum-utils
    $ sudo yum-config-manager     --add-repo     https://docs.docker.com/engine/installation/linux/repo_files/centos/docker.repo
    $ sudo yum makecache fast
    $ sudo yum -y install docker-engine
    $ sudo systemctl start docker
    $ sudo docker run hello-world
    $ sudo groupadd docker
    $ sudo usermod -aG docker $USER
    $ docker run hello-world

Installed Docker Compose according to https://docs.docker.com/compose/install/:

    $ curl -L https://github.com/docker/compose/releases/download/1.11.1/docker-compose-`uname -s`-`uname -m` > docker-compose
    $ chmod +x docker-compose
    $ sudo mv -i docker-compose /usr/local/bin/
    $ docker-compose --version


Right after this:

    $ docker-compose up -d
    Creating network "orr_default" with the default driver
    Pulling agraph (franzinc/agraph:latest)...
    latest: Pulling from franzinc/agraph
    a3ed95caeb02: Pull complete
    204c9415d16d: Pull complete
    15902c04210d: Pull complete
    7f336448cc07: Pull complete
    3aa9ab796a54: Pull complete
    648ffa1703df: Pull complete
    Digest: sha256:59b47aea1a8cc36fabe1b2cd4f8786d02b6f1830204f3233f8b7207a22626f14
    Status: Downloaded newer image for franzinc/agraph:latest
    Pulling mongo (mongo:latest)...
    latest: Pulling from library/mongo
    5040bd298390: Pull complete
    ef697e8d464e: Pull complete
    67d7bf010c40: Pull complete
    bb0b4f23ca2d: Pull complete
    8efff42d23e5: Pull complete
    3df9f20d1d07: Pull complete
    7b43ac0a1517: Pull complete
    010dcda0f65b: Pull complete
    ec68d17240b3: Pull complete
    Digest: sha256:0d4453308cc7f0fff863df2ecb7aae226ee7fe0c5257f857fd892edf6d2d9057
    Status: Downloaded newer image for mongo:latest
    Pulling orr-ont (mmisw/orr-ont:3.2.0)...
    3.2.0: Pulling from mmisw/orr-ont
    357ea8c3d80b: Pull complete
    52befadefd24: Pull complete
    42f3df327392: Pull complete
    3decae4e9763: Pull complete
    0a60a7e0c31d: Pull complete
    b42727ba883d: Pull complete
    98299a24213c: Pull complete
    4e86af554266: Pull complete
    c14bf4b895e0: Pull complete
    b3c6818facc8: Pull complete
    344704147e3d: Pull complete
    49d3b423158b: Pull complete
    8cf65b911807: Pull complete
    82fe415b4d6b: Pull complete
    3d0f3ff34d85: Pull complete
    Digest: sha256:fb41470b1402a6dca8e2195e75e5a6e9884fe14326f48d48d69ef08005539982
    Status: Downloaded newer image for mmisw/orr-ont:3.2.0
    Creating mongo
    Creating agraph
    Creating orr-ont

    $ docker logs -f --tail=300 orr-ont

The ORR is running.

