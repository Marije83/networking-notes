# mTLS
## What is mTLS
Mutual Transport Layer Security
- Ensures that parties at each end of a network connection are who they claim to be by verifying that they both have the correct private key (Cloudflare)
- a process that allows two parties to authenticate each other during the intial connection of an SSL/TLS handshake. It establishes an encrypted TLA connection in which both parties use X.509 digital certificates to authenticate each other (f5)
- Additional verification comes from their respective TLS certificates (Cloudflare)


## TLS vs mTLS
| TLS | mTLS |
| ----------- | ----------- |
| Client connects to server | Client connects to server |
| Server presents TLS certificate | Server presents its TLS certificate |
| Client verifies the server's certificate | Client verifies the server's certificate |
|  | Client presents its TLS certificate |
|  | Server verifies the client's certificate |
|  | Server grants access |
| Client and server exchange information over encrypted TLS connection | Client and server exchange information over encrypted TLS connection |

(based on cloudflare)

Another difference between TLS and mTLS ==when used within a single organisation?==:
- Organisation implementing mTLS is its own CA (as opposed to TLS which has an external CA)(cloudflare)
- Organisation creates own, self-signed Root TLS certificate to which certificates used by authorized clients and servers have to correspond.(cloudflare)

## Where is mTLS frequently used?
- Appropriate for close environments in which you have control over all users and/or endpoints (f5) 
- Often used in ZERO TRUST security framework ==like we have at THG== to verify users, devices and servers within an organization. (Cloudflare) (e.g. identify company-owned laptop, give access to corporate network from authorized device, client certificates stored on employee smart cards to give access to applications or areas/buildings, etc.) (f5)
- content delivery network (CDNs) or cloud security servives to back-end web servers, e.g. e-commerce platforms where visitors to website have TLS but CDN has mTLS connection to the origin web-server (f5) 

Can also be used to authorize:
- users into applications ==as long as the list of potential users is finite and controlled?==(f5)
- business-to-business data exchanges that use APIs (f5)
- Internet of Things sensors, i.e. remote traffic cameras or smart home devices such as thermostats (f5, SSL.com)
- Microservice architecture in which each microservice must ensure that each component it communicates with is valid and not tampered with (f5)
        
## Example application that uses mTLS
### Online banking (f5):
Revised Payment Services Directive for online banking:

The directive is intended to increase the security of online banking and boost innovation. One implication for customers is that they should be able use a single online banking app to access multiple accounts, even if they are from different banks. 
- One financial institution needs to query accounts held by another
- To make this secure banks often use mTLS either at the application layer or at the transport layer.
- E.g. Customer uses app from Bank A to manage accounts held by both Bank A and Bank B
- Bank A acts as client when querying customer accounts held by Bank B
- Bank A and Bank B have agreed to only trust certificates from specific CA, let's call it CA1
- If Bank C wants to query Bank A, it will be rejected as it does not have CA1 but CA2.

The general process for mTLS is the same for dealing with personal and sensitive financial data, but the vetting process for acquiring a certificate is much stricter.

## Cons and caveats of mTLS
- digital certificate is only as trustworthy as the process for identifying the individual requesting the certificate, but this process is not taken into account after a certificate has been issued. If it comes from a trusted source, it will be trusted, regardless of whether the process was robust or not ==though if processes were not robust, the CA would be less likely to be seen as a trusted CA ?== (f5)
- Private certificates can be stolen if used in untrustworthy environments. (f5)

## Diagram to show how mTLS works
![diagram showing how mTLS works](./mTLS.ping)

# References
- Cloudflare. 2023. What is mutual TLS (mTLS)? Available from [https://www.cloudflare.com/en-gb/learning/access-management/what-is-mutual-tls/] Last accessed on 30/10/2023
- F5. 2023. What is mLTS? Available from [https://www.f5.com/labs/learning-center/what-is-mtls] Last accessed on 30/10/2023
- SSL.com. 2023. Authenticating users and IoT devices with Mutual TLS. Available from [https://www.ssl.com/blogs/authenticating-users-and-iot-devices-with-mutual-tls/] Last accessed on 31/10/2023

