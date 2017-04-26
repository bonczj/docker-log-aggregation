# CentOS 7 Packaging
The installation script requires the use of the 'jq' CLI. On CentOS 7,
the jq command is only available via Extra Packages for Enterprise Linux (EPEL)
on CentOS. Manual instructions for enabling EPEL can be found at
[https://fedoraproject.org/wiki/EPEL]. For CentOS 7, this is as easy as
running the command *yum install epel-release*.

```
Install EPEL
$ yum install epel-release

Install jq dependency
$ yum install jq 

Install the FluentBit daemon
$ rpm -Uvh td-agent-bit-0.11.3-1.x86_64.rpm

Copy the FluentBit configuration for docker
$ cp [docker-log-aggregation]/etc/td-agent-bit/td-agent-bit.conf /etc/td-agent-bit/td-agent-bit.conf


Update docker daemon to always use the fluentd log driver
$ cat daemon.json
{
  "storage-driver": "devicemapper",
   "storage-opts": [
     "dm.thinpooldev=/dev/mapper/docker-thinpool",
     "dm.use_deferred_removal=true",
     "dm.use_deferred_deletion=true"
   ]
}
$ cat daemon-log-driver.json 
{
  "log-driver": "fluentd"
}
$ jq -s add daemon.json daemon-log-driver.json 
{
  "storage-driver": "devicemapper",
  "storage-opts": [
    "dm.thinpooldev=/dev/mapper/docker-thinpool",
    "dm.use_deferred_removal=true",
    "dm.use_deferred_deletion=true"
  ],
  "log-driver": "fluentd"
}
$ systemctl restart docker.service


Enable and start the docker-log-aggregation systemd service

Enable and start the FluentBit daemon service
$ systemctl enable td-agent-bit.service
$ systemctl start td-agent-bit.service
```