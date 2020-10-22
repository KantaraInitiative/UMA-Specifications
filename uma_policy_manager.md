



# UMA Policy Manager  

| | |
|:---|:---|
|Version:|0.1|
|Document Date:|2020-07-30|
|Editors:|Alec Laws|
|Contributors:||
|Produced by:|
|Status| |
|Abstract| |
|Suggested Citation| |

#### Notice
IPR Option: Non-Assertion Covenant
Copyright: The content of this document is copyright of Kantara Initiative, Inc.© 2020 Kantara Initiative, Inc.





## Abstract

This extension to UMA enables a resource owner to use a policy management service to manage the policy conditions, such as required requesting party claims, over their registered resources in order for the authorization server to grant access to a client.


## 1. Introduction

This document extends [UMA Fedz] in order to specify the interface provided by the AS to the RO for policy management. This is achieved by introducing a Policy Management API which is used by the Policy Manager to define resource owner policy rules.


The specification of this API enables many new use cases and benefits to an UMA ecosystem. 
By allowing The AS to delegate the policy management user experience and advice to another party

- the best place to decide policy may be inline with a resource registration, such as directly at the RS (trusted claims example.) 

- the RS can perform a more 'technical' or authority role to an ecosystem (ie less UX need)

- The RO may manage resources protected by many AS's from a single client application
  -  ie to have a complete view of their resources and policy
- The RO may be able to set their preferred policy management client with the AS
  - useful if AS is fixed to a service/resource ecosystem


- In conjunction with the Relationship Manager API (Resource Manager API?), the RO may protect a resource server at many ASs (?)


- select use cases

Use Case (trusted claims example)

Alice is using a photo sharing service to store her photo s online. Alice has already directed the service to use her AS to protect access to her photos. By requesting access to the policy API (ie when requesting a PAT...) the RS is able to provide the UX to allow Alice to share her photo with bob@kantara.com. This is possible without Alice to login and use the AS provided management panel.


Use Case (Authenticated Email)

Alice's email service supports the AEMS specification, this allows her to upload files to the email service which are then stored and protected by her personal data store and authorization server respectively. Through the email client, Alice is able to attach files in her email to Bob, and create policy for the Bob to download the attached files. When Bob's email service receives the email, it will automatically assert control of Bob's email to Alice's AS, allowing the mail client to download the file. This entire flow takes place without Alice or Bob needing to interact with an authorization server.


Use Case (Policy Advice)

Alice is able to use select and use a service independent from her AS (or her many ASs) to manage her access policy. This service may help Alice to create default policies or understand the implications of different policies.


Use Case (Domain AS)

Alice has selected two authorization server services which specialize in maintain resource and client ecosystem withing specific domain. The first AS allows her to connect her banks OB API to several financial application The second AS allows her protect her health records from several sources to a single personal health record application. Alice is able to have a single view of all her resources and a single policy management  

... Even if Alice is able to use her AS to protect all of her resources (ie trusted by RSs... and clients... and RqPs). This service does NOT need to be the primary user agent/experience that she interacts with. The policy manager API allows _any_ UI to allow Alice to manage resource access policy



--- older

This new client type is authorized to use the policy API after being authorized by the RO as the AS. THis is the same mechanism used by an RS to put resources under protection, the RS is issued a PAT by the AS.


This new client provides a few key benefits to an UMA ecosystems
- The AS may delegate policy management user expereince and advicce to another party
- The RO is able to choose this client, where they may not be able to choose an AS or RS. The RO receives a consistent policy management interface


For example, there are two UMA AS's, one being operated by the Alice's local health authority and one operated by Alice's personal Bank. Alice's resource servers are only able to accept authorization decisions from those RS, effectively Alice must use those RSs to protect her resources. When those AS's support this profile, they give back some agency to Alice in how she manages her resources and policy, and reduce their own need to provide this UX (maybe each AS provides on UMA Policy Manager by defaults, but also accept third party policy management services). Alice may then manage policy across all of her RS's and AS's from a single control panel. 





### 1.1 Notational Conventions

### 1.2 Abstract Flow

```
      +------------------+ 
      |     resource     | 
      |       owner      | 
      +------------------+ 
             |              
             |   
             v 
      +------------------+
      |     Policy       |
      |     Manager      |
      +------+-----------+
             |       
             |       
          manage (a PAT type authz?)   
             |              
             |              
             v              
       +------------------+   
       |   policy         |   
       |    API           |   
       +------------------+
       |                  |
       |   authorization  |
       |      server      |
       |                  |
       +------------------+
```

This specification uses all of the terms and concepts in [UMAGrant] and [UMA Fedz]. This figure introduces the following new concepts:

- UMA Policy Manager
- Policy API 		The API presented by the AS to the Policy Manager. This API is OAuth protected



### 1.3 HTTP Usage, API Security, and Identity Context


## 2 Authorization Server Metadata 

TBD


## Registered Resource Endpoint

The API available at the registered resources endpoint enables the policy manager to have knowledge of the resources under protection for the resource owner. 


### Registered Resource Description

A registered resource is a JSON document that extends the resource description from [UMA Fedz 3.1 Resource Description](1) with the following parameters:

resource_server REQUIRED A string identifying the resource server that hosts this resourcce

(1 https://docs.kantarainitiative.org/uma/wg/rec-oauth-uma-federated-authz-2.0.html#resource-set-desc)

### Registered Resource API

The authorization server must support the following resource management options, and must require a value RMT for access to them. Here, regresuri stands for the registered resource endpoint and \_id stands for the authorization server-assigned identifier for the web resource corresponding to the resource at the time it was created, included within the URL returned in the Location header.

- Read resource description: GET regresuri/\_id
- List resource descriptions: GET regresuri/

#### Read Registered Resource 

Reads a previously registered resource description using the GET method. If the request is successful, the authorization server MUST respond with an HTTP 200 status message that includes a body containing the referenced resource description, along with an \_id parameter.

Form of a read request, with a RMT in the header:
```
GET /regres/KX3A-39WE HTTP/1.1
Authorization: Bearer 4B7E-937E-FD64DA6F4C92
...
```

Form of a successful response, containing all the parameters that were registered as part of the description:

```
HTTP/1.1 200 OK
Content-Type: application/json
...
{  
   "_id":"KX3A-39WE",
   "resource_scopes":[  
      "read-public",
      "post-updates",
      "http://www.example.com/scopes/all"
   ],
   "icon_uri":"http://www.example.com/icons/sharesocial.png",
   "name":"Tweedl Social Service",
   "type":"http://www.example.com/rsrcs/socialstream/140-compatible",
   "resource_server": "https://tweedl.com"
}
```

#### List Registered Resource Description

Identical to the option defined in [UMA Fedz 3.2.5 List Resource Descriptions]()



## Policy Endpoint

The API available at the policy endpoint enables the policy manger to register resource owner requirements for claims presentation in order for a requesting party to be granted access to a protected resource.

The policy manager uses a RESTful API at the authorization server's  policy endpoint to create, read, update and delete policy descriptions, along with retrieving lists of such descriptions. The descriptions consist of JSON documents that are maintained as web resources at the authorization server.


[INSERT DIAGRAM HERE]
Figure TDB illustrates the policy API operations, with requests and success responses shown.




*??* should Alice be able to see policy use/application. ie "this policy was used on Jan 1 using these claims, it resulted in a RPT with these scopes: []"

### Policy Description


A policy is a JSON document, that describes the a policy sufficiently for the authorization server to make a decision for a resource request. A policy document MAY be provided as a signed JWT to ensure it's integrity to the RS during enforcement. A policy description has the following parameters


There may be many policies registered against the same resource



resource_id REQUIRED The resource id registered at the AS that this policy applies to

resource_scopes REQUIRED The approved scopes if the claims requirements are met

required_claims REQUIRED An array of claims/attributes that must be presented by the RqP in order to access this resource under this policy. Claims may be presented to the AS through many token formats, or gathered through interactive claims gathering



optional/extension/discussion:
sub The subject known by the RS for this RO (useful for general resource definitions)
resource_server (again, useful for general resource definitions)

client_id The client application that may be used to access this resource



For example... 

```
{
  resource_id: "KX3A-39WE",
  "resource_scopes": [
      "read-public",
      "post-updates",
  ],
  required_claims: [
      {  
         "claim_token_format":[  
            "http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
         ],
         "claim_type":"urn:oid:0.9.2342.19200300.100.1.3",
         "friendly_name":"email",
         "issuer":[  
            "https://example.com/idp"
         ],
         "name":"email23423453ou453"
      }


  ]
}
```





### Policy API 


The authorization server must support the following policy management options, and must require a value RMT for access to them. Here, poluri stands for the policy endpoint and \_id stands for the authorization server-assigned identifier for the web resource corresponding to the policcy at the time it was created, included within the URL returned in the Location header.

- Create policy: POST poluri/
- Read policy: GET poluri/_id
- Update policy: PUT poluri/_id
- Delete policy: DELETE poluri/_id
- List policy: GET poluri/








