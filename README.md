# Solution - Use Cases
Use Case API sets that can be layered into Ping Server Profiles

This repo provides Postman collections that can be applied to Server Profile stacks, or standalone servers. They do not contain files that need to be deployed into the containers.

---
# Use Cases
API collections that modify Server Profiles to do other things

## PingFederate
* [PF Base Config](#pingfederate-base-configuration)
* [PF CIAM Config](#pingfederate-ciam-configuration)
* [PF CIBA Config - [requires PF CIAM Config]](#pingfederate-ciba-config-requires-pf-ciam)

* [Generate PF Audit logs for SIEM](#Generate-PF-Audit-logs-for-SIEM)

## PingAccess
* [PA Proxy for PD \ PF - ACME Certificate](#pa-with-acme-managed-cert-for-pf--pd)

---
# Deployment
These use cases can be deployed in several ways:
* Postman --> Manual or Collection Runner
* Docker --> `docker run postman/newman ...`
* Compose \ K8s --> Service definition

---
## PingFederate Base Configuration
Server Profile: [PF-Base](https://github.com/cprice-ping/Profile-PF-Base)

This API collection takes the vanilla PF Configuration and wires up Applications and Authentication Experiences that can be used to quickly demonstrate PF capabilities.

[Documentation](https://documenter.getpostman.com/view/1239082/SWLh4RQB)

### Deployment
[Sample Environment File](pf_base_env_sample.json)

**Postman Collection**  

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/2e0df14dcf26f1ddb39a)

**Postman Newman**  
`newman run https://www.getpostman.com/collections/2e0df14dcf26f1ddb39a -e postman_vars.json --insecure --ignore-redirects`

**Newman - Docker**  
`docker run -d postman/newman run https://www.getpostman.com/collections/2e0df14dcf26f1ddb39a -e postman_vars.json --insecure --ignore-redirects -v ./postman_vars.json:/etc/newman/postman_vars.json`

**YAML**
```
pf-config-base:
    image: postman/newman
    command: run https://www.getpostman.com/collections/2e0df14dcf26f1ddb39a -e postman_vars.json --insecure --ignore-redirects
    volumes:
        # An environment file should be injected into the image - this file should contain your specfic info and secrets
        - ./pf_base_vars.json:/etc/newman/postman_vars.json
    networks:
      - pingnet-internal
```
**Note:** The `networks` need to match something the target service is using in the stack

---
## PingFederate CIAM Configuration
Server Profile: [PF-CIAM](https://github.com/cprice-ping/Profile-PF-CIAM)

This API collection takes the PF-Base Configuration and replaces the PingID Adapter \ MFA with PingID SDK.

[Documentation](https://documenter.getpostman.com/view/1239082/SWLk4kLF)

### Deployment
[Sample Environment File](pf_ciam_env_sample.json)

**Postman Collection**  

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/b17a3494b4f4d54de628)

**Postman Newman**  
`newman run https://www.getpostman.com/collections/b17a3494b4f4d54de628 -e postman_vars.json --insecure --ignore-redirects`

**Newman - Docker**  
`docker run -d postman/newman run https://www.getpostman.com/collections/b17a3494b4f4d54de628 -e postman_vars.json --insecure --ignore-redirects -v ./postman_vars.json:/etc/newman/postman_vars.json`

**YAML**
```
pf-config-ciam:
    image: postman/newman
    command: run https://www.getpostman.com/collections/b17a3494b4f4d54de628 -e postman_vars.json --insecure --ignore-redirects
    volumes:
        # An environment file should be injected into the image - this file should contain your specfic info and secrets
        - ./pf_ciam_vars.json:/etc/newman/postman_vars.json
    networks:
      - pingnet-internal
```
**Note:** The `networks` need to match something the target service is using in the stack

---
## PingFederate CIBA Config (Requires PF-CIAM)
Server Profile: [PF-CIAM](https://github.com/cprice-ping/Profile-PF-CIAM)

This API collection takes the PF-CIAM Configuration and adds a CIBA configuration:
* Email Authenticator
* PID SDK Authenticator
* CIBALogon OIDC Client

[Documentation](https://documenter.getpostman.com/view/1239082/SWTHZEe2)

### Deployment
[Sample Environment File](pf_ciam_env_sample.json)

**Postman Collection**  

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/246ba03433c2ffe26de0)

**Postman Newman**  
`newman run https://www.getpostman.com/collections/246ba03433c2ffe26de0 -e postman_vars.json --insecure --ignore-redirects`

**Newman - Docker**  
`docker run -d postman/newman run https://www.getpostman.com/collections/246ba03433c2ffe26de0 -e postman_vars.json --insecure --ignore-redirects -v ./postman_vars.json:/etc/newman/postman_vars.json`

**YAML**
```
pf-config-ciba:
    image: postman/newman
    command: run https://www.getpostman.com/collections/246ba03433c2ffe26de0 -e postman_vars.json --insecure --ignore-redirects
    volumes:
        # An environment file should be injected into the image - this file should contain your specfic info and secrets
        - ./pf_ciam_vars.json:/etc/newman/postman_vars.json
    networks:
      - pingnet-internal
```
**Note:** The `networks` need to match something the target service is using in the stack

---
## PA with ACME-Managed cert for PF / PD
Server Profile: N/A

Often theres a need to have certificates that are signed by a public CA. Things like Delegator or PingCentral use the OIDC Issuer for both backchannel calls (Metadata and token validation) and frontchannel (Application rendering and API calls). Not all products allow insecure TLS connections.   

PingAccess v6 included a feature to generate KeyPairs and have them managed with the ACME protocol and the Let's Encrypt service. This capability makes PA a nice way to include a proxy to a configuration to enable trusted certificates in front of other services.
 
[Documentation](https://documenter.getpostman.com/view/1239082/SWT5jLpF)

### Deployment
[Sample Environment File](pa_add_proxy_env_sample.json)

**Postman Collection**  

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/eaa397bd3a35ef3095c1)

**Postman Newman**  
`newman run https://www.getpostman.com/collections/eaa397bd3a35ef3095c1 -e postman_vars.json --insecure --ignore-redirects`

**Newman - Docker**  
`docker run -d postman/newman run https://www.getpostman.com/collections/eaa397bd3a35ef3095c1 -e postman_vars.json --insecure --ignore-redirects -v ./pa_add_proxy.json:/etc/newman/postman_vars.json`

**YAML**
```
pa-config-proxy:
    image: postman/newman
    command: run https://www.getpostman.com/collections/eaa397bd3a35ef3095c1 -e postman_vars.json --insecure --ignore-redirects
    volumes:
        # An environment file should be injected into the image - this file should contain your specfic info and secrets
        - ./pa_add_proxy.json:/etc/newman/postman_vars.json
    networks:
      - pingnet-internal
```
**Note:** The `networks` need to match something the target service is using in the stack

---
## Generate PF Audit logs for SIEM
Server Profile: N/A

This collection automates authentication events for SAML and OIDC requests. This will generate `audit.log` events that, when combined with a SIEM stack, can be used to demonstrate Dashboards.

[Documentation](https://documenter.getpostman.com/view/1239082/SzKSSzHU)

### Deployment

**Postman Collection**  

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/56c1cc10f3c607c6425b)

**Postman Newman**  
`newman run https://www.getpostman.com/collections/56c1cc10f3c607c6425b --env-var "pfBaseURL={{Your PingFed BaseURL}} --env-var "pfAdminURL={{Your PingFed AdminURL}}" --env-var "pfAdmin={{Your PF Admin}}" --env-var "pfAdminPwd={{Your PF Admin Password}}" --insecure --ignore-redirects --delay-request 500 -n 1000`

**Newman - Docker**  
`docker run -d postman/newman run https://www.getpostman.com/collections/56c1cc10f3c607c6425b --env-var "pfBaseURL={{Your PingFed BaseURL}} --env-var "pfAdminURL={{Your PingFed AdminURL}}" --env-var "pfAdmin={{Your PF Admin}}" --env-var "pfAdminPwd={{Your PF Admin Password}}" --insecure --ignore-redirects --delay-request 500 -n 1000`

**YAML**
```
pf-generate-authn:
    image: postman/newman
    command: run https://www.getpostman.com/collections/56c1cc10f3c607c6425b --env-var --env-var "pfAdminURL={{Your PingFed AdminURL}}" --env-var "pfAdmin={{Your PF Admin}}" --env-var "pfAdminPwd={{Your PF Admin Password}}" --insecure --ignore-redirects --delay-request 500 -n 1000
    networks:
      - pingnet
```
**Note:** The `networks` need to match something the target service is using in the stack