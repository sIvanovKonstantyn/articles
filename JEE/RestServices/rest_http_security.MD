#Http security for JEE Web application

## bullet points:
* define security roles and security constraints in web.xml
* use @RolesAllowed (or another annotation from javax.annotation.security pachage - it depends on what are you looking for)



## websphere using tests:

### application secutiry ON:

| client                 |  user mapped in websphere |  web xml properties  |  annotation on class   | cathed in request filter    | result |
---------------------------------------------------------------------------------------------------------------------------------------------
|send an existing user   | yes                       | auth-constrain - *   | role is requared       | yes, user principal is full | 200    |
|send an existing user   | yes                       | auth-constrain - *   | role is NOT wsrequared | yes, user principal is full | 403    |
|send an unexisting user | imposible                 | no metter            | no metter              | no                          | 401    |
|send an existing user   | no                        | auth-constrain - *   | imposible              | yes, user principal == null | 403    |




### application secutiry OFF:

| client                 |  user mapped in websphere |  web xml properties  |  annotation on class   | cathed in request filter    | result |
---------------------------------------------------------------------------------------------------------------------------------------------
|send any user           | no metter                 | auth-constrain - *   | role is requared       | yes, user principal == null | 403    |


