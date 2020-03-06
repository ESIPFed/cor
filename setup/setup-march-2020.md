# COR setup on new AWS machine - March 2020

In short, pretty much same steps as described in the April 2018 doc for
the underlying required software setup, and copy of directories, files,
mongo database, crontab from previous instance.

## Basic setup

    sudo yum update -y
    sudo yum install -y git
    sudo yum install -y docker
    sudo yum install -y java-1.8.0-openjdk-devel
    sudo yum install -y w3m
    sudo yum install -y httpd24

    sudo service docker start
    sudo service httpd start

    sudo usermod -a -G docker cor-admin1

(Also installed [`ctop`](https://github.com/bcicen/ctop#install).)

## COR

- transferred files and mongo database
- set `/etc/httpd/conf.d/orr.conf`
- transferred `/var/www/html/`
- adjusted file ownership/permissions

## sweetontology.net

- transferred `sweet_tools` and enabled cronjob
- set `/etc/httpd/conf.d/sweetontology.conf`


**Done.**

> Annie notified via Slack to proceed with IP cutover for `cor.esipfed.org` and `sweetontology.{net,org,info}`.
