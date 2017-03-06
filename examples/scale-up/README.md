# Microsoft Azure Container Service Engine - Scale up virtual machine availability set agent pools

## Overview

ACS-Engine enables you to add nodes to an existing cluster. For virtual machine scale sets agent pools this is just a simple change to the count number in the parameters file. 
This will also work for most availability set agent pools. availability set agent pools that have updated arm resources might run into issues depending on the behavior
of the underlying ARM RP, some of them treat resources in ARM templates as a PUT(replace) some treat them as a PATCH(update). This causes issues
in particular with the Kubernetes Azure driver. It updates the network resources and blindly putting will overwrite Kubernetes's changes and can cause 
outages in any services running on the agent pool and/or fail the update deployment (ARM) due contention in updating the resources. 
To get around this there is now an Offset parameter on each agent pool. If you want to scale up an existing cluster take the template 
you deployed orignally. Update the accompanying parameters file to have the Offset == old count and the Count == new desired number of vms. 
example: I originally deployed 5 vms in an agent pool. I want to scale up to 10. I would set Offset==5 and Count == 10. Then deploy the template 
in incremental mode.

Note: the Offset parameter has a default value and is not normally set.

Example template **kubernetes_tempalte.json** 
Orignal Parameters **kubernetes_orignal_params.json** 
Scale up parameters **kubernetes_scale_up_params.json** 
Show the reuse of a template with that was created with 6 nodes and scaled up to 15

To scale down a availability set agent pool the proper way to do it is to first list vms you want to delete and save 
their osdisk locations then delete the vms. Then delete the network interfaces and osdisks(not to do this you might 
need to get the access keys to the storage accounts).