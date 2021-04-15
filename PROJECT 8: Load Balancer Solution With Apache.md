 # Load Balancer Solution With Apache

Load balancing refers to efficiently distributing incoming network traffic across a group of backend servers, also known as a server farm or server pool.

Modern high‑traffic websites must serve hundreds of thousands, if not millions, of concurrent requests from users or clients and return the correct text, images, video, or application data, all in a fast and reliable manner. To cost‑effectively scale to meet these high volumes, modern computing best practice generally requires adding more servers. In our setup in Project-7, we had 3 Web Servers and each of them had its own public IP address and public DNS name. A client has to access them by using different URLs, which is not a nice user experience to remember addresses/names of even 3 server, let alone millions of Google servers.

In order to hide all this complexity and to have a single point of access with a single public IP address/name, a Load Balancer can be used. Benefits of load balancing include reduced downtime, scalability, redundancy, flexibility, and efficiency.


![image](https://user-images.githubusercontent.com/78841364/114626197-596d9480-9c81-11eb-8e7b-d3c761f468b2.png)




## References

https://www.nginx.com/resources/glossary/load-balancing/

