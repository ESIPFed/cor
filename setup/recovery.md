# Restarting COR services

## Option 1: Restart system

One path to restart COR services is simply to restart the COR AMI. The COR services should automatically be restarted.


## Option 2: Relaunch COR services

To simply relaunch the COR services, the recommended approach is to use docker-compose.

### Setup

The user should be logged in as cor-admin1 and change to the COR directory. Then they should execute the variables setup script.
```
[cor-admin1@ip-172-31-44-124 ~]$ cd COR
[cor-admin1@ip-172-31-44-124 COR]$ bash setenv.sh

## Bringing COR down
[cor-admin1@ip-172-31-44-124 COR]$ docker-compose down
```

### Starting COR up

To bring the system back up, use docker-compose up.

```
[cor-admin1@ip-172-31-44-124 COR]$ docker-compose up -t
```

## Option 3: Relaunch specific service

You can relaunch a specific service, or any combination, with docker. The dependency order should be followed to ensure services are ready for their clients. 
Your directory doesn't matter for this operation. [Note: I'm not 100% confident this approach will work under all conditions. jbg]

### Stopping services

From cor-admin1 (or ec2-user?), execute 'docker stop' and the service name(s), in the order orr, mongo, agraph.

```
[cor-admin1@ip-172-31-44-124 ]$ docker stop orr
orr
```

### Starting services

From cor-admin1 (or ec2-user?), execute 'docker stop' and the service name(s), in the order agraph, mongo, orr.

```
[cor-admin1@ip-172-31-44-124 COR]$ docker start agraph
agraph
[cor-admin1@ip-172-31-44-124 COR]$ docker start mongo
mongo
[cor-admin1@ip-172-31-44-124 COR]$ docker start orr
orr
```

