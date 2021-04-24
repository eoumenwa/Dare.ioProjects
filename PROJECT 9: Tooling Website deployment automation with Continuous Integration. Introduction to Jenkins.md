# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

In this project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub will be automatically be updated to the Tooling Website.


Continuous Integration
Continuous Delivery
Continuous Deployment.

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server

## Target setup

![image](https://user-images.githubusercontent.com/78841364/115937552-41a6c500-a466-11eb-8719-8b34014c106b.png)



## Summary
In this project, I learnt how to implement a Continous Integration solution using Jenkins CI which involved pushing commits directly from github automatically using a web-hook and also copying build artifacts over SSH from jenkins to the NFS-Server which also makes our webservers (being mnounted) to get updates to /var/www/html for website updates
