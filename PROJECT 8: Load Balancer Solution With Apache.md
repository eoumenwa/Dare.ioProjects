 # Load Balancer Solution With Apache

Load balancing refers to efficiently distributing incoming network traffic across a group of backend servers, also known as a server farm or server pool.

Modern high‑traffic websites must serve hundreds of thousands, if not millions, of concurrent requests from users or clients and return the correct text, images, video, or application data, all in a fast and reliable manner. To cost‑effectively scale to meet these high volumes, modern computing best practice generally requires adding more servers. In our setup in Project-7, we had 3 Web Servers and each of them had its own public IP address and public DNS name. A client has to access them by using different URLs, which is not a nice user experience to remember addresses/names of even 3 server, let alone millions of Google servers.

In order to hide all this complexity and to have a single point of access with a single public IP address/name, a Load Balancer can be used. Benefits of load balancing include reduced downtime, scalability, redundancy, flexibility, and efficiency.


![image](https://user-images.githubusercontent.com/78841364/114626197-596d9480-9c81-11eb-8e7b-d3c761f468b2.png)



Load balancing can be performed at various layers in the Open Systems Interconnection (OSI) Reference Model for networking. Here we offer an overview of two load‑balancing options at two different layers in the model.

Overview of Layer 4 and Layer 7 Load Balancing

Layer 4 load balancing operates at the intermediate transport layer, which deals with delivery of messages with no regard to the content of the messages. Transmission Control Protocol (TCP) is the Layer 4 protocol for Hypertext Transfer Protocol (HTTP) traffic on the Internet. Layer 4 load balancers simply forward network packets to and from the upstream server without inspecting the content of the packets. They can make limited routing decisions by inspecting the first few packets in the TCP stream.

Layer 7 load balancing operates at the high‑level application layer, which deals with the actual content of each message. HTTP is the predominant Layer 7 protocol for website traffic on the Internet. Layer 7 load balancers route network traffic in a much more sophisticated way than Layer 4 load balancers, particularly applicable to TCP‑based traffic such as HTTP. A Layer 7 load balancer terminates the network traffic and reads the message within. It can make a load‑balancing decision based on the content of the message (the URL or cookie, for example). It then makes a new TCP connection to the selected upstream server (or reuses an existing one, by means of HTTP keepalives) and writes the request to the server. Software L7 Application LB include Apache, NGINX or HAProxy)


In this project we will enhance our Tooling Website solution by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL.


![image](https://user-images.githubusercontent.com/78841364/114834409-84013f80-9d9e-11eb-8442-df85684d9828.png)


## Task

Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

### Prerequisites

Two RHEL8 Web Servers

One MySQL DB Server (based on Ubuntu 20.04)

One RHEL8 NFS server


![image](https://user-images.githubusercontent.com/78841364/114838408-b01ebf80-9da2-11eb-88b3-96acb9fafe14.png)


## Configure Apache As A Load Balancer

1. Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb
2. Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.
3. Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:
4. Verify that our configuration works - try to access your LB’s public IP address or Public DNS name from your browser:
5. Unmount /var/log/httpd/ from the Web Servers to the NFS server 
   ![image](https://user-images.githubusercontent.com/78841364/115362081-3108fd00-a18f-11eb-87c7-3647cc25adb1.png)

7. Open two ssh/Putty consoles for both Web Servers and run following command:
   sudo tail -f /var/log/httpd/access_log

8. Try to refresh your browser page http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php several times and make sure that both servers    
   receive HTTP GET requests from your LB - new records must appear in each server’s log file.
 
 
 

    ![image](https://user-images.githubusercontent.com/78841364/115430919-c7aadd80-a1d2-11eb-95e6-cdc7cad54001.png) 
     Web 1 responding to LB request
    
    
    
    ![image](https://user-images.githubusercontent.com/78841364/115431127-f628b880-a1d2-11eb-8219-4539dec3e58d.png)  
      Web 2 responding to LB request
     
     
     
     
     
     ![image](https://user-images.githubusercontent.com/78841364/115431320-2b350b00-a1d3-11eb-9b35-658a1a8208fa.png)  Ping stats from LB



## Final setup

![image](https://user-images.githubusercontent.com/78841364/115895702-b65b0e80-a428-11eb-90b7-d150f5ec07de.png)


## Summary
In this project, I learnt how to implement a Load balancing Web Solution and howto test the load balancing through the browser and the curl command.










## References

https://www.nginx.com/resources/glossary/load-balancing/
https://www.nginx.com/resources/glossary/layer-7-load-balancing/
https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04
https://linuxhint.com/how-to-install-and-configure-haproxy-load-balancer-in-linux/
https://unix.stackexchange.com/questions/524871/how-does-the-linux-command-mount-a-work


