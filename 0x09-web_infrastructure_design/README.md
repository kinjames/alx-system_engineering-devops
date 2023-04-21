0x09-web_infrastructure_design


What is a server?
A server is a computer system or a program that provides services or resources to other computers or programs (known as clients) over a network.

What is the role of the domain name?
A domain name is a human-readable identifier that is used to represent an IP address. It allows users to access websites using memorable names instead of IP addresses, which are hard to remember.

What type of DNS record www is in www.foobar.com?
The www in www.foobar.com is a subdomain. It is a type of DNS record that specifies a subdomain and the IP address it corresponds to. In this case, the www subdomain points to the IP address of the web server.

What is the role of the web server?
The web server is responsible for handling incoming requests from users and returning responses. It serves static content (such as HTML, CSS, and images) directly and forwards dynamic requests to the application server for processing.

What is the role of the application server?
The application server runs your codebase and is responsible for processing dynamic requests. It communicates with the database server to fetch data and return responses to the web server.

What is the role of the database?
The database stores and manages the data that your application needs. It provides a mechanism for storing and retrieving data efficiently, so your application can perform CRUD (Create, Read, Update, Delete) operations.

What is the server using to communicate with the computer of the user requesting the website?
The web server communicates with the user's computer using the HTTP protocol, which is a standard for transferring data over the Internet. It sends responses to the user's browser, which then renders the content for the user.

Now, let's talk about the issues with this infrastructure:

SPOF (Single Point of Failure)
Since we only have one server, it represents a single point of failure. If the server goes down, the website will become unavailable. To avoid this, we can introduce redundancy by adding more servers and load balancers.

Downtime when maintenance needed (like deploying new code web server

Two additional servers have been added to the infrastructure to allow for better load balancing and redundancy.
A load balancer (HAproxy) has been added to distribute incoming traffic across the two web servers. The load balancer is configured with a round-robin distribution algorithm, which means that it evenly distributes requests across all available servers in a cycle.
The load balancer enables an Active-Active setup, where both web servers are actively serving traffic. In an Active-Passive setup, only one server is actively serving traffic, while the other server acts as a backup.
The application server hosts the application code and is responsible for processing requests from the web server and communicating with the database.
The database is now a Primary-Replica (Master-Slave) cluster, with the Primary Node handling all writes and the Replica Node handling read-only queries. The Primary Node replicates all writes to the Replica Node to ensure data consistency.
The Primary Node and Replica Node have different roles in regard to the application. The Primary Node handles all write requests and updates the database accordingly. The Replica Node only handles read-only queries and returns the most up-to-date data from the Primary Node.
Now let me explain the issues with this infrastructure:

The load balancer is a single point of failure (SPOF), and if it fails, traffic will not be able to be distributed across the two web servers.
There are security issues as there is no firewall and no HTTPS configuration, leaving the infrastructure vulnerable to attacks and data breaches.
There is no monitoring in place to ensure that the infrastructure is running smoothly and to detect any potential issues before they become critical.


Three firewalls have been added to secure the infrastructure and protect it from external threats. One firewall is placed in front of each server to monitor and filter incoming traffic.
An SSL certificate has been added to serve www.foobar.com over HTTPS, which encrypts traffic between the server and the client, protecting sensitive information from being intercepted or stolen.
Three monitoring clients have been added to collect data on the infrastructure's performance and detect any potential issues before they become critical. The monitoring clients send data to a data collector, which can be Sumologic or another monitoring service.
The infrastructure is now secure, serves encrypted traffic, and is monitored to ensure that it is running smoothly and any issues can be detected and addressed quickly.
Now let me explain the issues with this infrastructure:

Terminating SSL at the load balancer level is an issue because it requires additional resources and increases latency. It is better to terminate SSL at the web server level or use a dedicated SSL offloading server to handle SSL encryption and decryption.
Having only one MySQL server capable of accepting writes is an issue because it creates a single point of failure (SPOF). If the Primary Node fails, the Replica Node will not be able to accept writes, causing downtime and data loss. It is better to have multiple MySQL servers that can handle writes and are set up in a Master-Master cluster configuration to ensure high availability.
Having servers with all the same components (database, web server, and application server) might be a problem because it creates a single point of failure (SPOF). If one component fails, the entire server will be affected. It is better to have a mix of different components to reduce the risk of failure and improve redundancy.
Finally, let me explain how to monitor web server QPS:

To monitor web server QPS, you can use a monitoring tool like Sumologic or another monitoring service that can collect data on the server's performance. The monitoring tool should be set up to collect data on the server's incoming requests and response times. You can then use this data to calculate the server's QPS and identify any potential performance issues.


In this infrastructure design, we will split the components into separate servers, including the web server, application server, and database server. Additionally, we will use a load balancer configured as a cluster with another load balancer for redundancy.

Requirements:

3 servers (web server, application server, database server)
2 load balancers (HAproxy) configured as a cluster for redundancy
Explanation:

Web server: The web server's primary responsibility is to handle HTTP requests from clients and return responses, such as web pages or files. It serves as an intermediary between the user's device and the application server.
Application server: The application server is responsible for running the server-side code that processes user requests and returns responses. It is where the application logic resides and communicates with the database to retrieve and store data.
Database server: The database server stores the application's data and responds to the application server's requests for data retrieval or storage.
Load balancer: The load balancer distributes incoming requests across multiple servers to ensure that no single server becomes overwhelmed with traffic. It is configured as a cluster with another load balancer to provide redundancy in case of failure or maintenance.
