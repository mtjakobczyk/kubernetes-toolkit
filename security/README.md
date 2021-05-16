
### Applications

#### Least Priviledge
Each discrete microservice has access only to the appropriate parts of a database. Even though products and users can be stored in the same database, the product microservices should be able to access and modify only product data.

#### Limiting the Attack Surface
The basic way to limit the attack surface is to minimize the amount of code or, more often, dependencies and keep them in the newest versions. This incurrs that it is crucial to be aware of the versions of subcomponents in use.

### Platform

:information_source: User who has all permissions on Kubernetes API is effectively the root user in the cluser.  
:point_up: Recommendation: know who can obtain full permissions on KUbernetes API
