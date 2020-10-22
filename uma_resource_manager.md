UMA Resource Server Management




This document extends [UMA Fedz] in order to specify the interface provided by the RS to the RO for resource management. This is achieved by introducing a Resource Management API which is used by the Resource Manager to view available resources and direct the AS to use for protection. 


** This API could also allows the RO to 
- view access history 
- set direct policy (ie Adrian clause)
- establish credentials required in AS



Reqs:

- THe RO must authenticate to the RS, in order to authorize access to this API 
	- (this capability of the RS is implied by UMA)

- The RO can see a list of resources available at this RS (either protected or not)
- The RO can modify the protection of resources (ie which AS to protect)

- The RO can see AS's available for protection
	- can the RO direct the RS to get a PAT from a new AS? This is tricky depending on how PAT's are issued (ie may require end-user redirection)





## Available Resources Endpoint

The API available at the available resources endpoint enables the resource manager to have knowledge of the resources hosted by the resource server for the resource owner. 



### Available Resource Description

A registered resource is a JSON document that extends the resource description from [UMA Fedz 3.1 Resource Description](1) with the following parameters:

authorization_server OPTIONAL A string identifying the authorization server that protects this resource
** could also be the AS policy uri from resource registration?

(1 https://docs.kantarainitiative.org/uma/wg/rec-oauth-uma-federated-authz-2.0.html#resource-set-desc)

#### List Available Resource Descriptions



#### Update Available Resources Description






## Authorization Server Endpoint


The API available at the authorization server endpoint enables the resource manager to view and manage the authorization servers available for resource protection. The resource owner may allow resource protection only through the set of available authorization servers. 

***note***: a challenge here is that it's quite difficult for this interface to create a relationship between an AS/RS because how the PAT is issued (ie could be interactive)



### Authorization Server Description

#### List Authorization Server Description

