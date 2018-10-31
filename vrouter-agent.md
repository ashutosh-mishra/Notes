##Vrouter Agent:
Vrouter Agent(Also known as VNsw agent) is responsible to manage dataplane component(vrouter kernel module).
- Interface with Controller to get the configuration
  Agent connects with controller via xmpp channel. Agent gets the configuration and converts it into the datapath understandable format.
  Agent manages the route info provided by Controller.
- Collect and export statistics from datapath
- Convert the data model provided by IFMap server in the datapath understandable format.
- Agent doesn't maintain DB, uses IFMap server to get info.

Agent modules are described in [Agent-README](https://github.com/Juniper/contrail-controller/blob/master/src/vnsw/agent/README)

## Rest Server
Agent maintains a REST server and exposed REST APIs through which user can add/delete interfaces in vrouter.

Interface can be added as:

```
vrouter-port-control --oper=add --vm_project_uuid=0281c3a7df464eb28130c88beaedf5d7  --instance_uuid=a2a2b0f3-890b-496f-8cbd-05bbd979c689  --vm_name=mc1   --uuid=a77fd887-a523-47ea-b149-aeffe524a0e7   --vn_uuid=e12140ca-cdb7-4759-b3d7-8e5f867f1b15  --port_type=NovaVMPort  --tap_name=enp132s0 --mac=02:a7:7f:d8:87:a5 --ip_address=15.0.0.3   --ipv6_address=   --tx_vlan_id=-1  --rx_vlan_id=-1
```

Interface can be deleted as:
```
vrouter-port-control --oper=delete --uuid=a77fd887-a523-47ea-b149-aeffe524a0e7
```

[vrouter-port-control](https://github.com/Juniper/contrail-controller/blob/master/src/vnsw/agent/port_ipc/vrouter-port-control) script is used to add tap interfaces in vrouter while launching vms. Refer [Contrail-Interface-Plugging](https://github.com/ashutosh-mishra/Notes/blob/master/Contrail-Interface-Plugging.md) doc for interface plugging in vrouter.


Interface add/delete can also be done via (vif command line](https://www.juniper.net/documentation/en_US/contrail3.2/topics/task/configuration/vrouter-cli-utilities-vnc.html#jd0e115) utility.

Curl commands to add the port
```
curl -X POST -H "Content-Type: application/json" -d '
{
    "id": "6eaf76b9-8b14-467d-a6ca-df39b3b0b54b",
    "type": 2,
    "instance-id": "707b51cf-a656-4f14-9b15-b583e809e5ad",
    "ip-address": "15.0.0.8",
    "display-name": "mc1",
    "plen": 24,
    "ip6-address": "::",
    "vn-id": "00a24d18-7ae7-4cd6-adc1-67005b13cdaf",
    "vm-project-id": "00000000-0000-0000-0000-000000000000",
    "mac-address": "32:6b:ea:7f:9a:24",
    "system-name": "enp2s0",
    "rx-vlan-id": -1,
    "tx-vlan-id": -1,
    "vhostuser-mode": 0,
    "dns-server": "15.0.0.2",
    "gateway": "15.0.0.1",
    "author": "/usr/bin/contrail-vrouter-agent",
    "time": ""
}'  http://192.168.6.240:9091/port


curl -X DELETE http://192.168.6.240:9091/port/6eaf76b9-8b14-467d-a6ca-df39b3b0b54c

curl -X GET http://192.168.6.240:9091/port/6eaf76b9-8b14-467d-a6ca-df39b3b0b54b

```



![port-add-rest-handler](/images/port-add-rest-handler.png)

![interface-on-change](/images/interface-on-change.png)



Different modules register for DB entry, which handle the entry updates accordingly.


### Vrouter

Vrouter-Agent maintains ksync module which is responsible for syncing data between agent and kernel module.
Functionality of Ksync:
- Keep operational stats of agent in-sync with kernel datapath
- Provides translation between agent and kernel datapath
- Keep agent platform independent



![process-event](/images/process-event.png)






