Setup CI/CD Pipeline For Node.js Application Using Visual Builder Studio
------------------------------------------------------------------------
In this tutorial you will learn deploying a Node Js application using DevOps tool Visual Builder Studio. 
Oracle Visual Builder Studio (VB Studio) is a DevOps and lifecycle management tool, and provides the infrastructure to help you build and deploy apps.

A VB Studio project is a collection of Git repositories, branch merge requests, wikis, issues, deployment configurations, and builds.

   
What Do You Need?

   -  A web browser
   -  Your Oracle Cloud account credentials
   -  Access to a VB Studio instance
   -  DEVELOPER_ADMINISTRATOR identity domain role assigned to you
   
   
   Reference URL for documentation : https://docs.oracle.com/en/cloud/paas/visual-builder/tutorial-create-project-import/

1. You can sign in to VB Studio from the Oracle Cloud console page.

  -  In a web browser's address bar, enter https://cloud.oracle.com/sign-in.
  -  In Cloud Account Name, enter your Oracle Cloud account name and click Next. 
  -  On the Oracle Cloud Account Sign In page, enter your Oracle Cloud username and password, and then click Sign In. 
  -  In the upper-left corner, click Navigation Menu Menu icon.
  -  Select More Oracle Cloud Services. Under Platform Services, select Developer.
     If you can't find the Developer option, select the Visual Builder Studio option.
  -  In the Instances tab, click Manage this instanceAction menu and select Access Service Instance. 

![image](https://user-images.githubusercontent.com/42166489/107630953-50187980-6c8a-11eb-98ba-eedd6cbcc6d2.png)


2. Create a Project

3. Collect these details and note down in a notepad - OCI Home Region Identifier
        OCI Region Key , Tenancy Name , Tenancy OCID , User Name , User OCID , User's Fingerprint , User's Auth Token , User's Email Address , User's Private Key
        Tanency Name, User Auth Key, OCI Region Key, User Auth Token
        
4. Create Build VM Template and Add Build VM
    - Open VB Studio and create VM Template. By default there are Java and required build template. Please add Docker and Node JS
    
5. Add the Docker File to the Git Repository

                    FROM node:14
                    ADD main.js ./
                    ADD package.json ./
                    RUN npm install
                    EXPOSE 80
                    CMD [ "npm", "start" ]
                    
6.         
