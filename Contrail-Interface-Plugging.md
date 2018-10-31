
## Openstack VM Launch

1. While launching VM, compute manager calls libvirt driver.
2. Libvirt driver invokes vif_driver's plug method(libvirt driver => vif_driver.plug())
3. VIF driver identifies if os-vif plugin exists
  a. OS-VIF Plugin path: get os-vif specific instance(nova_to_osvif_vif) to invoke os-vif path. VIF driver
  invokes os-vif plugin to invoke REST call to add add tap interface in vrouter.
4. Legacy path: VIF driver then invoke legacy non-os-vif path, which identifies and invoke the
corresponding implementation based on vif_type. VIF driver invoke REST call to add add tap interface in vrouter.

![Plugging Workflow](/images/os-vif-paths.png)
- Legacy path:
[Vif driver](https://github.com/openstack/nova/blob/master/nova/virt/libvirt/vif.py) directly invokes vendor drivers methods based on vif_type. Vendor driver
changes vif driver to add corresponding support.

- OS-VIF Plugin path:
Os-vif is a library for plugging and unplugging virtual interfaces (VIFs) in OpenStack. It provides:

- Plugin model to provide vendor specific plugin for plug/unplug vifs. Currently plugins supported for two networking backends - Open vSwitch and Linux Bridge. Only two plugins are included, all plugins for other networking backends are maintained in separate code repositories.
- Versioned objects that represent various types of virtual interfaces and their components.

Basically os-vif defines a common model for representing VIF types in OpenStack.

Nova vif driver checks for os-vif object, which should be defined in [os_vif_util](nova/network/os_vif_util.py).
Object will contains vnic_type with corresponding port-profile, device address, device type and plugin details.

Contrail's os-vif plugin is defined in [vrouter.py](https://github.com/Juniper/contrail-nova-vif-driver/blob/master/vif_plug_vrouter/vrouter.py#L132) which is maintained in [contrail-nova-vif-driver](https://github.com/Juniper/contrail-nova-vif-driver) repo. After installing setup, mentioned os-vif plugin will be installed at /opt/plugin/site-packages.



### Nova VIF Plugging:
Currently, Nova supports multiple types of Contrail plugging: TAP plugs, vhost-user socket plugs or VEB SR-IOV plugs. Neutron and the Contrail controller decides what VIF type to pass to Nova based on the Neutron port semantics and the configuration of the compute node. This VIF type is then passed to Nova:

- The 'vrouter' VIF type plugs a TAP device into the kernel vrouter.ko datapath.
- The 'vhostuser' VIF type with the 'vhostuser_vrouter_plug' mode plugs into the DPDK-based vRouter datapath.
- The 'hw_veb' VIF type plugs a VM into the VEB datapath of a NIC using vfio-pci passthrough.

Each neutron port has a vnic-type. Contrail supports following methods for vRouter VIF plugging:
- Normal: Standard kernel based plugging, or vhost-user based plugging depending on the datapath running on the hypervisor.
- Direct: PCI passthrough plugging into the VEB of an SR-IOV NIC.
- Virtio-forwarder: DPDK vhost user mode.


**Summary of plugging methods:**

Existing plugging methods supported by Contrail os-vif plugin:
```
- Legacy mode:
  - VIF type: hw_veb
    - VNIC type: direct

  - VIF type: vrouter
    - VNIC type: normal

- OS-VIF plugin mode:
  - VIF type: vhostuser (os-vif plugin: contrail_vrouter)
    - VNIC type: normal
      - Details: DPDK vhost-user plugging
      - os-vif object: VIFVHostUser

  - VIF type: vrouter (os-vif plugin: vrouter)
    - VNIC type: normal
      - Details: Classic kernel plugging (vrouter.ko) via TAP device
      - os-vif object: VIFGeneric
```



## SRIOV support
To enable SR-IOV support in Tungsten Fabric, following config need to add in instances.yaml file under vrouter section
```ansible
vrouter:
	SRIOV: true
	SRIOV_VF: 3
	SRIOV_PHYSICAL_INTERFACE: eno1
	SRIOV_PHYS_NET: physnet1
```

To launch a SRIOV VM:

create a neutron port as below and use the created port to launch vm.

```bash
neutron port-create network-name --name sriov_port --binding:vnic_type direct
```
While creating neutron port, vnic_type need to pass as "direct", which maps to vif type as "hw_veb".


