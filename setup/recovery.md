# Restarting COR services

## Option 1: Restart system

One path to restart COR services is simply to restart the COR AMI. The COR services should automatically be restarted.


## Option 2: Relaunch COR services

To simply relaunch the COR services, the recommended approach is to use docker-compose. 
(At some point docker should be upgraded, then it's `docker compose`.)

### Setup

The user should be logged in as cor-admin1 and change to the COR directory. Then they should execute the variables setup script.
(Note for improvement: use a docker .env file for the environment variables.)
```
[cor-admin1@ip-172-31-44-124 ~]$ cd COR
[cor-admin1@ip-172-31-44-124 COR]$ source setenv.sh
```

## Bringing COR down
To stop COR operations:

```
[cor-admin1@ip-172-31-44-124 COR]$ docker-compose down
```

### Starting COR up

To bring the system back up, use `docker-compose up -d`.

```
[cor-admin1@ip-172-31-44-124 COR]$ docker-compose up -d
```

## Option 3: Relaunch specific service

You can relaunch a specific service, or any combination, with docker. The dependency order should be followed to ensure services are ready for their clients. 
Your directory doesn't matter for this operation. [Note: I'm not 100% confident this approach will work under all conditions. jbg]

(These instructions will change with new docker, to  `docker compose stop <service>`)

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

# Recovering files

The directory /home/cor-admin1/COR is under version control using git (only local, no remote). 
So, recovering a file like setenv.sh would be a matter of just using git (see examples).

To recover a particular file, run
```
git checkout -- setenv.sh 
```

To see differences in a file that's been edited (like setenv.sh in this example):
```
$ git diff setenv.sh
diff --git a/setenv.sh b/setenv.sh
index 5e1c758..e54b2fc 100644
--- a/setenv.sh
+++ b/setenv.sh
@@ -1,4 +1,5 @@
 # Define these environment variables prior to running your ORR instance.
+# Reconstructed JBG 20250819 (from COR_TEST directory) after accidentally overwriting

 # Configuration directory on the host
 export HOST_CONFIG_DIR=$PWD/config
@@ -9,7 +10,7 @@ export ORR_HOST_DATA=$PWD/orr_data
 # Host port for the ORR service.
 # Make sure it is an unused port on your host.
 export ORR_HOST_PORT=9090
-
+#
 # Host Mongo data directory
 export MONGO_HOST_DATA=$PWD/mongo_data
```
