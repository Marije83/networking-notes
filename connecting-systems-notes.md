# Load balancing
    ## What is load balancing and how does it work

        "method of distributing network traffic equally across a pool of resources that support and application". "Directs and controls internet traffic between the application servers and their visitors or clients" (AWS)
        "efficiently distrinbuting incoming network traffic accross a group of backend servers" (NGINX)
            User requests to an application first go to the load balancer, which then routes each request to a single server in a server farm according to a chosen algorithm (see algorithms below) (AWS)
    
        ### Load balancers
            "a device that sits between the user and the server group and acts as an invisible facilitator" (AWS)
            "acts as athe 'traffic cop' sitting in front of your servers and routing client requests across all servers capable of fulfilling those requests in a manner of that maximizes speec and capacity utilizatio  and ensures that no one server is overworked" (NGINX)
            Private key for SSL certificate is installed on loadbalancer and SSL termination occurs on the load balancer (i.e. where it is decrypted), and connection between balancer and server is unencrypted (slides).
        
        ![Load balancing diagram (NGINX)](https://www.nginx.com/wp-content/uploads/2014/07/what-is-load-balancing-diagram-NGINX.png)
   
    ## Types of load balancing
        ### Application load balancing
                looks at request content to redirect traffic (e.g. HTTP headers or SSL session IDs) so that specific functions can be dealth with by specific servers (e.g. browse request on shopping site to server that can show images and videos but no need for open connections, or shopping cart requests to servers that can maintain many client connections and save cart data). (AWS) 
                ==So do not use one of the static or dynamic algorithms???==
        ### Network load balancing
                examine IP address and other network information to redirect traffic. Track the source of application traffic and can assign a static IP address to several servers using one of the algoritms below (AWS)
        ### Global server load balancing
                Occurs accross geographically distributed servers. Local balancers manage app load within a region or zone and attempt to redirect according to geographic proximity (AWS)
        ### DNS load balancing
                Configures domain to route requests across pool of resources on that domain (e.g. website, e-mail, print server, etc). Domains often have multiple IP addresses which are all returned when a clients requests reolutio of the domain name. As most clients used the first IP address they receive, that would scew the balance if the list was always in the same order, so DNS load balancing randomises the order in which IP addresses are returned. However, less reliably since it does not check if server on IP is working properly. (NGINX)
   
    ## Load balancing algorithms
        Different algorithms have different benefits so choice depends on needs (NGINX)
        ### Static load balancing
            follow fixed rules and are independent of current server state (AWS)
            - (weighted) Round-robin: servers take it in turns. With weighted, you can set some servers to take on more requests than others (to have their turn more frequently or to get multiple requests per turn?) (AWS)
                Distributed sequentially (NGINX)
            - Hash: distributes based on key you define (e.g. request URL)
            - IP hash: converts client IP address to a number, which is mapped to individual servers (AWS). 
                Advantage is that it allows for session persistance as all request from same client is sent to same server. However, does consume more RAM to keep communication channels open (slides)
        ### Dynamic load balancing
            examines current state of servers before distributing traffic
            - (weighted)least connection: load balancer checks which server has fewest active connections (communication channel between client and server) and directs traffic there. Again, weighting allows some servers to get sent more connections than others. Method assumes all connections require equal processing power (AWS)
            - Least repsonse time: combines server response time (total time to process incoming requests and send a response) and active connections to determine best server (AWS, NGINX)
            - Random with two choices: Picks two random servers and then applies least repsonse time to determine which of the two to send to (NGINX)
            - Resource based: analyses current server load via specialised software (agent). Usage of server resources is calucalted (e.g. computing capacity and memory) to ensure a server has enough resources to handle the request (AWS)
    
    ## Seven-Layer Open System Interconnection (OSI)
    ### Layer 4
        uses information defined at the networking transport layer as basis for deciding how to distribute client requests (e.g. source and destination IP addresses and ports). Often used by hardware load balancer (see below). Is faster than Layer 7 balancing, but nowadays the advantage is negligible in most situations (NGINX)
        Frequently used for network load balancing (Slides)
    ###Layer 7  
        uses information from the application layer (HTTP header, e.g. URL, type of data or cookie info). It is more CPU-intensive than packet-based Layer 4, buy on modern servers rarely causes degraded performance (NGINX)
        enables smarter decisions and optimizations and changes to the content (e.g. as it can read what type of data is requested, it can redirect to a server where that data is stored, meaning not all server have to be exact duplicates) (NGINX)
        Used for Application balancing (slides)
    
    ## Load balancing techonology
        ### Hardware
            securely processes and redirects gigabytes of traffic to hundreds of servers. Can be used to virtually creat multiple load balancers that are centrally managed. (AWS)
            Are expensive (initial investment) and need maintenance. Might not be used to full capacity and cannot handle traffic beyond its capacity (AWS)
        ### Software 
            applications that perform all load balancing functions. Can be installed on any server or accessed as third-party service (e.g. AWS) (AWS)
            Cheaper and more flexible (scale up and down according to need) and more compatible with modern cloud envbironments.
    ## Pros
       -  Improves app's availability: can detect servor problems and redirect to available servers --> decreases impact of server issues on app downtime (AWS)
            If a server goes down, traffic gets redirected to remaining online servers (NGINX)
            == But what if the load balancer fails, see discussion of cons below==
       - Improves app's scalability: prevents traffic bottlenecks, can predict traffic (AWS)
       - Improves app's security: have own built-in security features to add to existing security; redirect attack traffic to multiple servers to minimize impact; route traffic through firewalls; block malicious content (AWS)
       - Improves app's performance: increases response time and reduces network latency through distributing load evenly, redirect client requests to geographically closer servers. (AWS)
       - Provides flexibility to add or subtract servers as demand dictates ==(if using a software or third-party service load balancer)== (NGINX)
       
    ## Cons
        - Not all load balancers are equally good at session persistance (storing information about a user's session between requests'). This might not be a problem (e.g. for browsing) but can be a big problem for handling shopping sessions where all requests from a single client need to be sent to the same server  or storing information in cache to boost streaming performance. (NGINX)
        - Not all load balancers can dynamically add or remove servers from the group without interrupting existing sessions, so if you want to make use of the flexibility mentioned in the cons, you need to make sure your load balancing software is compatible (NGINX)
        - Load balancer is added component to the system and the more components, the less reliable (one more element that can fail). Adding a second load balancer to create a balancer cluster makes it more reliable as one can take over if the other fails. However, additional complexity, additional cost for hardware, and less secure as private key has to be in multiple places (slides)
            session stickiness: when there are multiple balancers, both balancers have to track state and share it between each other, which could lead to individual users experiencing a failing system if one of the loaders fails, eventhough the whole system might be available (slides)
 
 
# Proxy servers
    "a server application that acts as an intermediary between a client requesting a resource and the server providing that resource. It improves privacy, security, and performance in the process" (Wikipedia, Proxy server)
    Proxy servers may reside on the user's local computer or anywhere else between the user's computer and the destination server (Wikipedia, Proxy server, MDN)
    'facilitate access to content on the World Wide Web' (MDN)
    ## forward proxies
        internet-facing proxy used to retrieve data (Wikipedia, Proxy server)
        Used to block access to content (Slides)
        Protects IP address of client (Slides) ==if anynoymous proxy, but not for transparent ones???==
        repeated requests to a server for same content can be cached locally (slides)
        Some sources suggest they are also called tunneling or gateway proxies, which would suggest that they pass unmodified requests and responses (e.g. MDN)
        Provides services to (group of) clients, such as storing and forwarding internet services to reduce and control bandwith (MDN)
    ## reverse proxies
        internal-faxing proxy used as a front-end to control and protect access to a server on a private netwok. Generally appears to be ordinary server and are installed in vicinty of one or more webservers. Often does load-balancing (Wiki, slides, MDN), authentication, decryption (SSL termination, slides), compression (Wikipedia, MDN), security as server's IP address is hidden (slides), and caching (slides, MDN). (Wikipedia, Proxy server)
        Acts on behalf of servers, rather than clients.
    ## gateway or tunneling proxies
            passes unmodified requests and responses. (Wikipedia, Proxy server)
    ## open proxy
            is accessible by any internet user. Anonymous open proxies do not disclose source IP address. Transparent open proxy can show source IP address. (Wikipedia, Proxy server)
    
    ## Uses of proxy servers
    - monitoring: collecting data on URLs accessed by specific machines in your network; collect virus information etc (Okta)
    - improve: load distributing and content caches (Okta, but see reverse proxies)
    - translate: deliver response in right language based on client's IP address (Okta)
    - Hide: can block IP address (e.g. company's unique address, employees addresses, client's address) (okta)
    - Secure: can protect against some types of web server attacks (okta)
    - Steal: can be used by malicious actors to eavesdrop, as proxy protections can be manipulated or hackers set up their own proxies. Content between user and server might then be seen by a hacker. (okta)

#Firewalls
    "a security device that can help protect your internet network by filtering unknown traffic and blocking outsiders from gaining access to your private data" and can also protext from malicious software (Norton)
    "restrict access to a network via configuration rules" (slides), such as allow access to my network from other network on port xyz. Denies access based on src or destination IP address. (slides)
    ==Norton mentions 'other advanced internal rules' to determine reliability, but does not go into detail==
    A firewall looks at data packets and decides if it can continue or should be blocked based on where it came from and/or where it is going (BU)
    Packets can be accepted, denied or dropped, though denying is uncommon as it uses up bandwidth on its return trip, and because there is little added value to it (BU)
    
    ![Your network makes request to internet --> firewall scans and filters te data --> Your network granted access to requested source](https://us.norton.com/content/dam/blogs/images/norton/am/how-a-firewall-works.png)
        
    ##Firewall technology
    Can be software or hardware (slides, Norton, BU), or cloud-based (FaaS, Firewall as a service), which can grow with your organisation and o well with perimeter security (like hardware). (Norton)
    Many devices have some built-in firewall (Norton)
    Generally placed at edge of systemm, though some designs also have rules that restrict traffic between subnets (e.g. between Application and Data submet) (Slides)
    
    | Type of Firewall | Purpose |
    | ---------------- | ------- |
    | Packet-filtering firewall | basic for small networks; usually software; blocks network traffic IP protocol, IP addresses and port numbers; does not block web-traffic so vulnerable to web-based attacks (Norton) |
    | proxy service firewall | filters communication between your network and outside networks at the application layer; usually software; uses stateful and deep-packet inspection tech to analyze incoming traffic (Norton) |
    | stateful multi-layer inspection firewall | Administrators can add extra filters; keeps a database of vetted connections; usually hardware; (Norton) more complex than other firewalls (so requires more resources, but also allows more complex rules)(BU) |
    | unified threat management firewall | similat to SMLI firewalls with anitivirus and intrusion prevention; usually software; sometimes also includes cloud management (Norton) |
    | next-generation firewall | advanced capabilities that extensively inspect potential threats; usually software; inspects both header and content/source (others only header?); ability to block advanced malware (Norton) |
    | NAT firewalls | Blocks any unsolicited attempts to access your network; usually software (Norton) |
    | Virtual firewall | Basic; operates over cloud; can manage both physical and virtual networks (Norton) |
    | Application firewall | specifically coded for the type of traffic it is inspecting (e.g. a web application firewall) (BU); bases decisions on data in the packet rather than source and destination addresses (BU); allows for more sophisticated rules (e.g. limit nr of requests, slides, or limit nr of characters URL BU); understands type of traffic it is receiving and can make sensible decisions (e.g. to avoid SQL Injection attack) (slides);
        can react quickly to DDoS attacks (slides) |
    
    ## Host vs network based firewalls
    **network firewalls** filter traffic going between the internet and secured local area networks (LAN) (Norton) implemented at a specified point in the network path (BU)
        - used for large networks (Norton) and protects all computers inside the wall from computers outside the wall (BU)
        - May be installed on the perimeter to protect from hosts on the internet or internally to protect one segment of the network from another (e.g. separating corporate and residential systems, or research from marketing systems) (BU)
        - Cannot protect from other computers on the same network or from itself (BU)
        - monitors communications between company's computers and outside sources (Norton)
        - restricts certain websites and IP addresses (Norton)
        
    ** host-based firewalls** stored locally on a single computer or device.
        - allows for more customization (Norton)
        - installed on each server (Norton)
        - control incoming and outgoing traffic (Norton)
        - decides whether or not to allow traffic to individual devices (Norton) wheter the traffic comes from the intenet, the local network or from itself (BU)
        - protects the host (Norton)
        
# VPNs and NATs
    VPN: virtual private network. Disguises IP address when using public networks (Norton)
    NAT: network address translation: Creates secur and public IP address for all devices on the same network (Norton)

# References
AWS. 2023. What is Load Balancing. Available from [AWS What is Load Balancing](https://aws.amazon.com/what-is/load-balancing/) Last accessed on 16/10/2023 
BU TechWeb. 2023. How Firewalls Work. Boston University. [BU TechWeb Firewalls] (https://www.bu.edu/tech/about/security-resources/host-based/intro/) Last accessed on 17/10/2023 
MDN. 2023. Proxy servers and tunneling. Available from [MDN web docs_ Proxy serves and tunneling] (https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling) Last accessed on 16/10/2023
NGINX. 2023. What Is Load Balancing. Available from [NGINX What is Load Balancing] (https://www.nginx.com/resources/glossary/load-balancing/) Last accessed on 16/10/2023
Norton. 2023. What is a firewall. Firewalls explained and why you need one. [Norton firewall] (https://us.norton.com/blog/privacy/firewall) Last accessed on 17/10/2023
okta. 2023. Understanding Proxy Servers and How They Work. Available from [okta proxy servers](https://www.okta.com/identity-101/proxy-server/) Last accessed on 17/10/2023
Wikipedia. 2023. Proxy server. Available from [Wikipedia Proxy server](https://en.wikipedia.org/wiki/Proxy_server) Las access on 16/20/2023

