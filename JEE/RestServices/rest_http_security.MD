#Http security for JEE Web application


![scheme](https://github.com/sIvanovKonstantyn/articles/blob/master/JEE/RestServices/JEE_Security.jpg)

## bullet points:
1. Client sets the auth type and auth properties into header parameter "Authorization". For "basic" auth it seems like "Basic " + "user:password_that_base64_encoded";
2. Application server assumes the biggest part of security implementation:
    * SecurityContext implementation (javax.security.ws.rs.core.SecurityContext that gives principal checking/ auth scheme cheking/ secure connection cheking inteface);
    * Principal implementation (java.security.Proncipal that gives access to security Principal);
    * Cheking Proncipal for access to url (this mathing should be define in web.xml file. Example: [web.xml example](https://github.com/sIvanovKonstantyn/articles/blob/master/JEE/RestServices/web.xml)). If Principal is not matching and is another role was matched for this url - app. server returns 403;

3. In Application you could define more detail security rules by using @RolesAllowed (or another annotation from javax.annotation.security pachage - it depends on what are you looking for)


## Links
* [about security constraints in web xml](https://docs.oracle.com/cd/E19798-01/821-1841/bncbk/index.html)
* [annotations](https://www.oracle.com/technetwork/articles/javaee/security-annotation-142276.html)



# Test notes: http security with WAS and JEE

### application secutiry ON:

| client                 |  user mapped in websphere |  web xml properties  |  annotation on class   | catched in request filter    | result |
|------------------------|---------------------------|----------------------|------------------------|-----------------------------|--------|
|send an existing user   | yes                       | auth-constrain - *   | role is requared       | yes, user principal is full | 200    |
|send an existing user   | yes                       | auth-constrain - *   | role is NOT wsrequared | yes, user principal is full | 403    |
|send an unexisting user | imposible                 | no metter            | no metter              | no                          | 401    |
|send an existing user   | no                        | auth-constrain - *   | imposible              | yes, user principal == null | 403    |




### application secutiry OFF:

| client                 |  user mapped in websphere |  web xml properties  |  annotation on class   | catched in request filter    | result |
|------------------------|---------------------------|----------------------|------------------------|-----------------------------|--------|
|send any user           | no metter                 | auth-constrain - *   | role is requared       | yes, user principal == null | 403    |


















