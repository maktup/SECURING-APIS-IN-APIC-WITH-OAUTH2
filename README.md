## **SECURING APIS IN APIC WITH OAUTH2**

**1. CONTEXT:**
*"We all know that security at the API level is very important, to provide our clients with assurance regarding APIs that are publicly exposed for consumption"*.

Having an **IBM API CONNECT** license as an **API GATEWAY** platform facilitates this assurance, since it has internal mechanisms which allow all **OAUTH2 FLOWS** to be implemented currently.

**2. USE CASE: **
I am sharing how to show how to implement an architectural security solution, based on **OAUTH2 + additional components**, that allows having a robust solution for a client that manages a service-oriented architecture, in this case based on **REST** services.

**3. ARQUITECTURA: **
The architectural solution integrates the following:

**"JWT - AUTHORIZATION CODE + OPENID CONNECT"**, considering the use of a **DEFAULT HTML FORM** provided by **IBM API CONNECT**.

The architectural diagram of the solution is as follows:

![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/ARCHITECTURE/Arquitectura%20-%20%5BPropuesta-02%5D%20-%20%5BDefault%20HTML%20Form%5D.jpg?raw=true)


**4. ARCHITECTURE: **
The step-by-step procedure carried out for the **CONFIGURATION** and for the **TESTS** carried out of the architectural solution will be shown below:

This shows the creation of the **USER REGISTRY URL**, which will be used to access the **BackEnd URL** that will handle client authentication. This solution should be implemented on-site by the client, because it interacts with its **DB** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_01.jpg?raw=true)

Here is the creation of the **OAUTH PROVIDER** called: **ibm-native-oauth-provider8**, which will handle all the configuration associated with **OAUTH2** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_02.jpg?raw=true)

Here is the configuration to work with the flow: **AUTHORIZATION CODE**, which in **APIC** is called: **ACCESS CODE** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_03.jpg?raw=true)

The configuration of the **SCOPEs** to control the level of access allowed is shown here:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_04.jpg?raw=true)

The configuration for the correct handling of the **DEFAULT HTML FORM** is shown here, so that **IBM API CONNECT** creates a **LOGIN** interface for the user to authenticate manually:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_05.jpg?raw=true)

The **INTROSPECT** configuration is shown here, that will be used in the validation block defined in the architecture, to validate the: **ACCESS_TOKEN** generated:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_06.jpg?raw=true)

The configuration of the **OPENID CONNECT **is shown here, that will be used in the validation block defined in the architecture, to validate the: **ID_TOKEN** generated:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_07.jpg?raw=true)

The configuration of the **SECURITY SCHEMAS** (at the API level) is shown here, where the API that you want to secure is associated with the **OAUTH FLOW** configured in the **OAUTH PROVIDER** and the security plugin through the **ClientId / ClientSecret** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_08.jpg?raw=true)
 
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_09.jpg?raw=true)

**HERE THE TESTS IS STARTING**... of the service after being **PUBLISHED**. Here the URL of **/oauth2/authorize** , copied and taken to a URL (because it cannot be tested directly from **POSTMAN** ), since there is a redirect:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_10.jpg?raw=true)

Then, the browser's **TEST** tools are activated and the URL is executed, here we proceed to **AUTHENTICATE** with the **USER/PASSWORD** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_11.jpg?raw=true)

Then, when executing, we see that there are several **REDIRECTIONS**, the one that matters to us is **302** , since that is the one that if when **AUTHENTICATING** against the **BACKEND** service that was defined in the **USER REGISTRY URL**, it has responded correctly with **200 CODE**, we will obtain Here is a field in the **RESPONSE** **HEADER** called **LOCATION**, within this will be the **CODE** value with the **TOKEN** that is needed and that configures that the **AUTHENTICATION** has been correct:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_12.jpg?raw=true)

It is important to mention that this **CODE** value is the main objective of this **OAUTH2** flow that is used as the basis for the architectural proposal that has been chosen:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_13.jpg?raw=true)

Here the following **URL** is executed: **/api-jwt-generate/gen-01** , which is an **API** (that has been shared), which allows a **JWT** to be **hot-Generated** with the information desired by the client:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_14.jpg?raw=true)

Here the following **URL** is executed: **/oauth2/token**, which based on the **CODE** and the other parameters shown, allows generating the: **ACCESS_TOKEN** and the **ID_TOKEN**, each with a different objective at the flow level:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_15.jpg?raw=true)

Here the following **URL** is executed: **api-jwt-validate/val-01**, which is an API (that has been shared), which allows you to **hot-validate** the **JWT Tokens **(the initial one and the **ID_TOKEN**) to obtain the encrypted information and view if associated with the authenticated **USER** :
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_16.jpg?raw=true)

![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_17.jpg?raw=true)

Here the following **URL** is executed: **/oauth2/introspect**, that allows validating the value of the **ACCESS_TOKEN** obtained (before obtaining the **RESOURCE / API** ) and validating if it is associated with the authenticated USER:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_18.jpg?raw=true)

Finally, after the entire validation block has been carried out and with the **TOKENs** obtained, you can proceed with the **ACCESS_TOKEN** to obtain the response from the **RESOURCE/API**, which was the main objective:
![alt text](https://github.com/maktup/SECURING-APIS-IN-APIC-WITH-OAUTH2/blob/main/CONFIGURATION/Config_19.jpg?raw=true)

**5. COMPONENTS:**
For easy replication of the solution, the .**YAMLs** of the **APIs** used and the **OAUTH PROVIDER** have been shared with all the configuration already applied.
 
