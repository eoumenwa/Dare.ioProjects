# Devops Tooling Website Solution

This project describes a set of DevOps tools that will help a team in day to day activities in managing, developing, testing, deploying and monitoring different projects. This single DevOps Tooling Solution consists of:

1. Jenkins - free and open source automation server used to build CI/CD pipelines.

2. Kubernetes - an open-source container-orchestration system for automating computer application deployment, scaling, and management.

3. Jfrog Artifactory - Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.

4. Rancher - an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.

5. Grafana - a multi-platform open source analytics and interactive visualization web application.

6. Prometheus - An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting      approach.

7. Kibana - Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

Some Definitions

* Network-attached storage (NAS) is a file-level (as opposed to block-level storage) computer data storage server connected to a computer network providing 
  data access to a heterogeneous group of clients. A NAS unit is a computer connected to a network that provides only file-based data storage services to  
  other devices on the network. Although it may technically be possible to run other software on a NAS unit, it is usually not designed to be a general-
  purpose server. For example, NAS units usually do not have a keyboard or display, and are controlled and configured over the network, often using a 
  browser.

  A full-featured operating system is not needed on a NAS device, so often a stripped-down operating system is used. For example, FreeNAS or NAS4Free, both   open source NAS solutions designed for commodity PC hardware, are implemented as a stripped-down version of FreeBSD.

  NAS systems contain one or more hard disk drives, often arranged into logical, redundant storage containers or RAID.

  NAS uses file-based protocols such as NFS (popular on UNIX systems), SMB (Server Message Block) (used with MS Windows systems), AFP (used with Apple     
  Macintosh computers), or NCP (used with OES and Novell NetWare). NAS units rarely limit clients to a single protocol.    
   
* A storage area network (SAN) or storage network is a computer network which provides access to consolidated, block-level data storage. SANs are primarily   used to access data storage devices, such as disk arrays and tape libraries from servers so that the devices appear to the operating system as direct-     attached storage. A SAN typically is a dedicated network of storage devices not accessible through the local area network (LAN).

* Network File System (NFS) is a distributed file system protocol originally developed by Sun Microsystems (Sun) in 1984, allowing a user on a client   
  computer to access files over a computer network much like local storage is accessed. 
  
* In computer networking, Server Message Block (SMB), one version of which was also known as Common Internet File System (CIFS /sɪfs/), is a    
  communication protocol for providing shared access to files, printers, and serial ports between nodes on a network. It also provides an authenticated     
  inter-process communication (IPC) mechanism. Most usage of SMB involves computers running Microsoft Windows, where it was known as "Microsoft Windows  
  Network" before the introduction of Active Directory. Corresponding Windows services are LAN Manager Server for the server component, and LAN Manager  
  Workstation for the client component. 
  
* In computing, the SSH File Transfer Protocol (also Secure File Transfer Protocol, or SFTP) is a network protocol that provides file access, file   
  transfer, and file management over any reliable data stream. It was designed by the Internet Engineering Task Force (IETF) as an extension of the Secure   Shell protocol (SSH) version 2.0 to provide secure file transfer capabilities. The IETF Internet Draft states that, even though this protocol is   
  described in the context of the SSH-2 protocol, it could be used in a number of different applications, such as secure file transfer over Transport Layer   Security (TLS) and transfer of management information in VPN applications.

* In computing, iSCSI is an acronym for Internet Small Computer Systems Interface, an Internet Protocol (IP)-based storage networking standard for   
  linking data storage facilities. It provides block-level access to storage devices by carrying SCSI commands 
  over a TCP/IP network. iSCSI is used to facilitate data transfers over intranets and to manage storage over long distances. It can be used to transmit  
  data over local area networks (LANs), wide area networks (WANs), or the Internet and can enable location-independent data storage and retrieval.
  The protocol allows clients (called initiators) to send SCSI commands (CDBs) to storage devices (targets) on remote servers.
  
* Block-level storage is a concept in cloud-hosted data persistence where cloud services emulate the behaviour of a traditional block device, such as a  
  physical hard drive. It is a form of network-attached storage (NAS).
  Storage in such is organised as blocks. This emulates the type of behaviour seen in traditional disks or tape storage through storage virtualization.   
  Blocks are identified by an arbitrary and assigned identifier by which they may be stored and retrieved, but this has no obvious meaning in terms of  
  files or documents. A filesystem must be applied on top of the block-level storage to map 'files' onto a sequence of blocks.

  Amazon EBS (Elastic Block Store) is an example of a cloud block store. Cloud block-level storage will usually offer facilities such as replication for   
  reliability, or backup services.
  
* Object storage (also known as object-based storage) is a computer data storage architecture that manages data as objects, as opposed to other storage       architectures like file systems which manages data as a file hierarchy, and block storage which manages data as blocks within sectors and tracks. Each     object typically includes the data itself, a variable amount of metadata, and a globally unique identifier. Object storage can be implemented at multiple   levels, including the device level (object-storage device), the system level, and the interface level. Object storage systems allow retention of massive   amounts of unstructured data. Object storage is used for purposes such as storing photos on Facebook, songs on Spotify, or files in online collaboration   services, such as Dropbox.

## Solution components:

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub

References
https://en.wikipedia.org/wiki/Network-attached_storage
https://en.wikipedia.org/wiki/Storage_area_network
https://en.wikipedia.org/wiki/Network_File_System
https://en.wikipedia.org/wiki/Server_Message_Block
https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol
https://en.wikipedia.org/wiki/ISCSI
https://en.wikipedia.org/wiki/Block-level_storage
https://en.wikipedia.org/wiki/Object_storage
https://dzone.com/articles/confused-by-aws-storage-options-s3-ebs-amp-efs-explained

  
 
