# AMI Migration Planning

The COR needs to be migrated to a current Amazon Machine Instance, as the current system is running on Amazon Linux 1 (2018), a long-unsupported system.

## Notional Plan

* create a VM (let's call it new here), with docker and apache on it (and any other things you may need, eg for cert purposes)
* install the ORR https://mmisw.org/orrdoc/install/ 
  * those instructions should still be mostly accurate
  * note that you would use docker compose commands instead of docker-compose ones ...
* From the current VM (let's call it base here) to be used as the "reference" installation (maybe the one at mmisw or COR)
  * copy the orront.conf file
  * stop the ORR there at base
  * create tarball of the mongo database directory
  * create tarball of other assets (which I'm not remembering at the moment)
  * restart the ORR there at base
  * transfer the tarballs to new
  * if running, docker compose down on new
  * unpack the tarballs in the corresponding places on new
  * review orront.conf again
  * docker compose up on new
  * test, adjust configs, re-start, repeat
    * adding new and updated ontology
    * https (and http) accesses
    * administrative functions
    * add account, group
    * API operations
* Once tested and confirmed operational in new location
  * add static IP to new address   
