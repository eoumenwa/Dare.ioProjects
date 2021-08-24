## Continuous Integration with Jenkins | Ansible | Artifactory | SonarQube | PHP

![image](https://user-images.githubusercontent.com/78841364/124540541-97e09e80-dded-11eb-9983-245e58b2d25a.png)

### Version Control: 
This is the stage where developers’ code gets committed and pushed after they have tested their work locally.

### Build: 
Depending on the type of language or technology used, we may need to build the codes into binary executable files (in case of compiled languages) or just package the codes together with all necessary dependencies into a deployable package (in case of interpreted languages).

### Unit Test: 
Unit tests that have been developed by the developers are tested. Depending on how the CI job is configured, the entire pipeline may fail if part of the tests fails, and 
developers will have to fix this failure before starting the pipeline again. A Job by the way, is a phase in the pipeline. Unit Test is a phase, therefore it can be 
considered a job on its own.

### Deploy: 
Once the tests are passed, the next phase is to deploy the compiled or packaged code into an artifact repository. This is where all the various versions of code including the 
latest will be stored. The CI tool will have to pick up the code from this location to proceed with the remaining parts of the pipeline.

Auto Test: Apart from Unit testing, there are many other kinds of tests that are required to analyse the quality of code and determine how vulnerable the software will be to external or internal attacks. These tests must be automated, and there can be multiple environments created to fulfil different test requirements. For example, a server dedicated for Integration Testing will have the code deployed there to conduct integration tests. Once that passes, there can be other sub-layers in the testing phase in which the code will be deployed to, so as to conduct further tests. Such are User Acceptance Testing (UAT), and another can be Penetration Testing. These servers will be named according to what they have been designed to do in those environments. A UAT server is generally be used for UAT, SIT server is for Systems Integration Testing, PEN Server is for Penetration Testing and they can be named whatever the naming style or convention in which the team is used. An environment does not necessarily have to reside on one single server. In most cases it might be a stack as you have defined in your Ansible Inventory. All the servers in the inventory/dev are considered as Dev Environment. The same goes for inventory/stage (Staging Environment) inventory/preprod (Pre-production environment), inventory/prod (Production environment), etc. So, it is all down to naming convention as agreed and used company or team wide.
Deploy to production: Once all the tests have been conducted and either the release manager or whoever has the authority to authorize the release to the production server is happy, he gives green light to hit the deploy button to ship the release to production environment. This is an Ideal Continuous Delivery Pipeline. If the entire pipeline was automated and no human is required to manually give the Go decision, then this would be considered as Continuous Deployment. Because the cycle will be repeated, and every time there is a code commit and push, it causes the pipeline to trigger, and the loop continues over and over again.
Measure and Validate: This is where live users are interacting with the application and feedback is being collected for further improvements and bug fixes. There are many metrics that must be determined and observed here. We will quickly go through 13 metrics that MUST be considered.



![image](https://user-images.githubusercontent.com/78841364/130381287-3198e762-809e-4793-b8fc-09ae9420f207.png)
Artifactory status

![image](https://user-images.githubusercontent.com/78841364/130384400-87c4bc07-68e5-41c4-8d14-bf1aae602149.png)
Artifactory Landing

![image](https://user-images.githubusercontent.com/78841364/130585651-53c3e88b-f60a-4002-9415-4993aee4b4e1.png)
jenkins-artifactory config

### References

https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-cname-record/

https://aws.amazon.com/blogs/compute/deploying-an-nginx-based-http-https-load-balancer-with-amazon-lightsail/

https://www.howtoforge.com/tutorial/ubuntu-jfrog/

https://websiteforstudents.com/how-to-install-jfrog-artifactory-on-ubuntu-18-04-16-04/

https://cloudcone.com/docs/article/the-linux-wget-command/
