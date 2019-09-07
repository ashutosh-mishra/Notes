


## Shared VPC(also referred as XPN) - 
Allows multiple projects to use a common VPC network. Basically expose subnets from the one project(host project) to other projects(service projects) in the same organization.
Instances can communicate across via internal IPs.
Shared VPC connects project within the same organization. Host and service projects cannot belong to different organizations.




### Provision Shared VPC:
Designate a project as host project (All networks of host projects are shared VPC networks), required Shared VPC Admin permission to do this operation,
Attach 1 or more service projects to host project, required Shared VPC Admin permission to do this operation,


### Required Administrative roles:
**1. Organisation Admin**

   Can assign Shared VPC Admin role

**2. Shared VPC Admin**

   Setup shared VPC, enable host project, attach service projects to host project.
   Delegate access to some or all subnets in shared VPC network to Service Project Admins. 

**3. Service Project Admin(compute.networkUser role)**

   A shared VPC Admin defines Service Project Admins by granting permission to use the all host project subnets or some of the host project subnets.

   Service project Admins can create instances in Service projects using the subnets of host project.


### Usage:

Networks - Can be identified only via shared VPC

1. List Subnets :

   GET https://www.googleapis.com/compute/v1/projects/HOST_PROJECT_ID/aggregated/subnetworks/listUsable

   https://cloud.google.com/compute/docs/reference/rest/v1/subnetworks/listUsable

2. Create Instances

   Instance creation arguments is same as of non-shared VPC, with same restriction i.e. zone should belong to the subnet’s region
   Only difference is while providing networkInterfaces, network interfaces should be provided as follows, where project ID should be of host project ID.

   ``` python
   "networkInterfaces": [
       {
         "subnetwork": "projects/HOST_PROJECT_ID/regions/REGION/subnetworks/SUBNET_NAME"
       }
   ],
   ```

3. VPC Peering:

   VPC peering with shared network is allowed only via host project. Service project can not establish VPC peering on shared network.
