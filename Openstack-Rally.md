
## Openstack rally

Rally is a openstack testing tool, which checks openstack working/performance at scale.
Rally unifies openstack deployment, verification, testing and profiling.

Rally provides:
1. Measuring and profiling
2. Detection of scaling and performance issues
3. Service Level Agreements(SLA), to ensure minimum/maximum threshold for success/failure rates
4. Plugin based architecture - to easily extend new/existing test cases without modifying rally’s core functionality
5. Its own DB, for storing task and corresponding statuses
6. Generates reports in various html, json formats


**Rally Architecture**:
Rally can run, either:
1. **As a service**   :  Runs rally as a set of daemons, like any other openstack services
2. **As a CLI App**  :  Runs rally as a cli app, via cli commands, which directly invokes rally core


#### Rally plugins:
1. **Context as a plugin**
Context plugins runs before any scenario iteration starts. This can be used to create pre-requisite resources for scenarios.
For Ex a custom flavour can be created, which will be used in scenario testing(before scenario testing starts) and will be deleted as part of context plugin cleanup.

2. **Scenario runner as a plugin**
Runs a given scenario a random no of times(random from a given range)

3. **Scenario as a plugin**
Run a specific scenario, like CRUD nova/neutron resources

4. **SLA as a plugin**
Service Level Agreement plugin, which checks/validates for success criteria via given values.

5. Hooks. Hook trigger plugins



Recommended approach is to create a plugin, instead of modifying existing functionality.


#### To create a plugin:    
Plugin can be created by inheriting plugin class from *plugin.Plugin* class or any of its subclasses.


There are 2 openstack rally projects:    
**rally** - [https://github.com/openstack/rally](https://github.com/openstack/rally) - Rally is tool & framework that allows one to write simple plugins and combine them in complex tests scenarios that allows to perform all kinds of testing.    
**rally-openstack** - [https://github.com/openstack/rally-openstack](https://github.com/openstack/rally-openstack)- Collection of rally plugins used for openstack platform. These existing plugins support different neutron resources CRUD operations.


Following are 2 sample plugins:
1. Based on **plain rally openstack scenario plugin**, which creates network, subnet
```
from rally import consts
from rally.plugins.openstack import scenario
from rally.task import atomic
from rally.task import validation


@validation.add("required_services", services=[consts.Service.NOVA])
@validation.add("required_platform", platform="openstack", users=True)
@scenario.configure(name="ScenarioPlugin.create_net")
class CreateNetwork(scenario.OpenStackScenario):
    """Sample plugin which create net ."""

    @atomic.action_timer("create_network")
    def _create_network(self, net_name):
        """ Create network via REST API client """
        nets = self.clients("neutron").list_networks()
        self.clients("neutron").create_network({"network": {"name": net_name}})
        print 'Networks list: ', nets




    def run(self, net_name):
        """List networks and create a new network"""
        self._create_network()

```


2.  Based on **rally-openstack plugin**, which creates network, subnet and launch a server:

```
from rally_openstack.scenarios.neutron import utils
from rally_openstack.scenarios.nova import utils as nova_utils
@validation.add("required_platform", platform="openstack", users=True)
@scenario.configure(name="ScenarioPlugin.create_net_sub")
class NeutronNetSubTest(utils.NeutronScenario, nova_utils.NovaScenario):

    def run(self, image, flavor, network_create_args, subnet_create_args=None):
        network, subnet = self._create_network_and_subnets(network_create_args, subnet_create_args)
        print ‘Created resources: network: %s, subnet: %s’ % (network, subnet)
        kwargs = {}
        kwargs.update({'nics': nics})
        self._boot_server(image, flavor, **kwargs)


—————————————

# Corresponding JSON file
# cat create_net_sub.json 
{
    "ScenarioPlugin.create_net_sub": [
        {
            "args": {
                "image": "4788dd2f-6a4b-4347-995a-d1d6f07284c3",
                "flavor": "1",
                "network_create_args": {"name": "ash-test-4"},
                "subnet_create_args": {"cidr": "10.0.0.0/29", "name": "ash-test-4"}
            },
            "runner": {
                "type": "serial",
                "times": 1
            },
            "sla": {
                "failure_rate": {
                    "max": 0
                }
            }
        }
    ]
}

```



Difference between 1st and 2nd sample plugin    
__*1st plugin*__ - Uses rest API client to execute CRUD operations. Default rally project exposes scenario plugin for openstack which provides rest API client, which can be used to execute any openstack resource API.    
__*2nd plugin*__ - Uses existing rally-openstack plugin which internally uses this client to provide neutron resources CRUD operations. We can directly invoke self._create_network() by passing proper arguments. Existing plugin provide precreated functions for CRUD APIs.    


Local plugin can be invoked by providing in cli option or placing at /root/.rally/plugins path.
```
rally --plugin-paths /rally/plugins/foo.py,/rally/plugins/bar.py ...
```

Any specific task can be started as:
```
# rally task start create_net_sub.json

# rally —-plugins-path <absolute-path>/custom-plugin.py task start create_net_sub.json  # with custom plugin
```
