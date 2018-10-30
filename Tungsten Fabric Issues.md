Tungsten Fabric container issues:


- **Installation:**

Tungsten Fabric on Openstack setup can be installed via [Contrail-with-Openstack-Kolla.](https://github.com/Juniper/contrail-ansible-deployer/wiki/Contrail-with-Openstack-Kolla)

For any installation issue follow - [Provisioning-F.A.Q](https://github.com/Juniper/contrail-ansible-deployer/wiki/Provisioning-F.A.Q)

NOTE: For nodes with memory less then or equal to 16GB - refer - [controller-has-16gb-or-less](https://github.com/Juniper/contrail-ansible-deployer/wiki/Provisioning-F.A.Q#12-vm-for-controller-has-16gb-or-less-demo-version-setup-is-not-stable-all-memory-is-consumed-by-several-java-apps)

- **Status:**

Once installation is successfull, check contrail-status for all the services
If any node is in "Error" or "Initializing" state, check corresponding logs in /var/log/contrail/*.

Typical output of contrail-status:
```
root@contrail-PowerEdge-T630:~# contrail-status:
Pod              Service         Original Name                          State    Status
                 redis           contrail-external-redis                running  Up 18 minutes
analytics        alarm-gen       contrail-analytics-alarm-gen           running  Up 12 minutes
analytics        api             contrail-analytics-api                 running  Up 16 minutes
analytics        collector       contrail-analytics-collector           running  Up 17 minutes
analytics        nodemgr         contrail-nodemgr                       running  Up 12 minutes
analytics        query-engine    contrail-analytics-query-engine        running  Up 15 minutes
analytics        snmp-collector  contrail-analytics-snmp-collector      running  Up 15 minutes
analytics        topology        contrail-analytics-topology            running  Up 15 minutes
config           api             contrail-controller-config-api         running  Up 16 minutes
config           device-manager  contrail-controller-config-devicemgr   running  Up 18 minutes
config           nodemgr         contrail-nodemgr                       running  Up 12 minutes
config           schema          contrail-controller-config-schema      running  Up 18 minutes
config           svc-monitor     contrail-controller-config-svcmonitor  running  Up 18 minutes
config-database  cassandra       contrail-external-cassandra            running  Up 17 minutes
config-database  nodemgr         contrail-nodemgr                       running  Up 15 minutes
config-database  rabbitmq        contrail-external-rabbitmq             running  Up 18 minutes
config-database  zookeeper       contrail-external-zookeeper            running  Up 17 minutes
control          control         contrail-controller-control-control    running  Up 18 minutes
control          dns             contrail-controller-control-dns        running  Up 18 minutes
control          named           contrail-controller-control-named      running  Up 17 minutes
control          nodemgr         contrail-nodemgr                       running  Up 12 minutes
database         cassandra       contrail-external-cassandra            running  Up 18 minutes
database         kafka           contrail-external-kafka                running  Up 15 minutes
database         nodemgr         contrail-nodemgr                       running  Up 13 minutes
database         zookeeper       contrail-external-zookeeper            running  Up 17 minutes
vrouter          agent           contrail-vrouter-agent                 running  Up 11 seconds
vrouter          nodemgr         contrail-nodemgr                       running  Up 12 minutes
webui            job             contrail-controller-webui-job          running  Up 18 minutes
webui            web             contrail-controller-webui-web          running  Up 17 minutes

WARNING: container with original name 'contrail-external-redis' have Pod or Service empty.

Pod: '' / Service: 'redis'. Please pass NODE_TYPE with pod name to container's env

vrouter kernel module is PRESENT
== Contrail control ==
control: **initializing**
nodemgr: **initializing (NTP state unsynchronized. )**
named: active
dns: active

== Contrail config-database ==
nodemgr: initializing (NTP state unsynchronized. )
zookeeper: active
rabbitmq: active
cassandra: active

== Contrail database ==
kafka: active
nodemgr: initializing (NTP state unsynchronized. )
zookeeper: active
cassandra: active

== Contrail analytics ==
snmp-collector: active
query-engine: active
api: active
alarm-gen: active
nodemgr: initializing (NTP state unsynchronized. )
collector: active
topology: active

== Contrail webui ==
web: active
job: active

== Contrail vrouter ==
nodemgr: **initializing (NTP state unsynchronized. )**
agent: **initializing (XMPP:control-node:192.168.2.136 connection down, No Configuration for self)**

== Contrail config ==
svc-monitor: active
nodemgr: initializing (NTP state unsynchronized. )
device-manager: active
api: active
schema: active

```

Above status has following errors:
1. NTP state unsynchronized

This can be resolved by providing proper NTP server entries in /etc/ntp.conf. Restart ntp service after update.

2. Control node initializing, and vrouter-agent control node connection is down.

This can be resolved by restarting control-node container. Control node has xmmp connection with vrouter, and if 
control node is not working well, vrouter show 'No configuration for self'.

Controller can stuck in initializing state due to various issues:

  - Cassandra for config node is not working, 
    - Issue might be due to corrupted rpmdb, in that case you'll observe following log in /var/log/contrail/contrail-config-database-nodemgr.log

```
Error getting contrail-nodemgr package version.
```

This can be resolved by rebuilding rpmdb. To fix a corrupted RPM database:
```
$ rm -rf /var/lib/rpm/__db*
$ db_verify /var/lib/rpm/Packages
$ rpm --rebuilddb

I would also run the following commands to clear out YUMs caches and test the rebuilt RPM database:
$ yum clean all
$ rpm -qa
```

  - Cassandra not running
  
Try following in cassandra container to check if cassandra is ok, if not restart container
```
$ nodetool -p 7201 info
$ nodetool -p 7201 status
```

  - Cassandra permissions issues
  
Give the user you want permissions to the data and commitlog dirs.
```
sudo chown -R cassandra:cassandra /var/lib/cassandra/data
sudo chown -R cassandra:cassandra /var/lib/cassandra/commitlog
```


