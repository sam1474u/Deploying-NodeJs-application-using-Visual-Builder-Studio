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

![image](https://user-images.githubusercontent.com/42166489/107632945-3d537400-6c8d-11eb-98a5-386507daf4be.png)



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
                    
![image](https://user-images.githubusercontent.com/42166489/107632222-39732200-6c8c-11eb-84d6-2f42b14d1bf3.png)


6. Configure a Build Job 

   - Provide name and description
   - Select Git repo 
   - Add Step. Docker login, Docker build , Docker Push
   
![image](https://user-images.githubusercontent.com/42166489/107632920-32004880-6c8d-11eb-8289-00844d292570.png)

7. Run the Node.js App

   - On your computer, open the Docker terminal.Run this command to log into OCIR: 
            docker login iad.ocir.io
  -  When prompted, enter the user name and the auth token as the password. The password isn't visible when you type it. Press Enter when you're done.
  - Run this command to pull the Docker image you pushed to OCIR from the build job: 
            docker pull iad.ocir.io/myaccount/ociuser/my_nodejs_image
            
  - Wait for the image to download. 
  - Run this command to publish the image to a local container: 
  
    
![image](https://user-images.githubusercontent.com/42166489/107633454-0e89cd80-6c8e-11eb-961a-ce46d7f3bcb7.png)

![image](https://user-images.githubusercontent.com/42166489/107633477-18133580-6c8e-11eb-8656-2c63c6041e33.png)

![image](https://user-images.githubusercontent.com/42166489/107633484-1ea1ad00-6c8e-11eb-9810-0a56bb7ad659.png)
  
 ![image](https://user-images.githubusercontent.com/42166489/107633616-54df2c80-6c8e-11eb-8a32-f4f9fa4f272a.png)

![image](https://user-images.githubusercontent.com/42166489/107633631-5ad50d80-6c8e-11eb-8196-ed831fa497ec.png)


8. Update the VM Template to add Kubernetes
9. Add the Kubernetes File to the Git Repository

apiVersion: apps/v1
kind: Deployment
metadata:
  name:  nodejsmicroappocir-k8s-deployment
spec:
  selector:
    matchLabels:
      app:  nodejsmicro
  replicas: 1
  template:
    metadata:
      labels:
        app: nodejsmicro
    spec:
      containers:
      - name: nodejsmicro
        image: bom.ocir.io/bmdrgwy1wsjh/viveklal/my_nodejs_image:latest
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: ocirsecret
---
apiVersion: v1
kind: Service
metadata:
  name:  nodejsmicroappocir-k8s-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nodejsmicro


10. Configure a Build Job 
   Enter the name and from Add step, select OCIcli
   Enter required details which you noted in notepad earlier. Please note the private key saves as * for security.
   
   -----BEGIN RSA PRIVATE KEY-----
MIIABCD123efgh456XYz321UAT654ORA1290A2b3C4d5hym28a8b1c0AyugOFsdg
cbNF375h59hjgfdDGN4n5ji9J85VH75544FGHHJk9HJK98seFe45Da==
-----END RSA PRIVATE KEY-----
   
   
![image](https://user-images.githubusercontent.com/42166489/107634542-b358da80-6c8f-11eb-88db-8ae534747a52.png)
   
   
   In the Steps tab, from Add Step, select Common Build Tools, and then select Unix Shell.
   Copy this shell script and paste it in Script. 
     
          mkdir -p $HOME/.kube
         oci ce cluster create-kubeconfig --cluster-id ocid1.cluster.oc1.iad.abcdefghiaaaaaaabbbbbbb09876m8n9o0abcd1234901ghi0n6o7pababcd --file $HOME/.kube/config --region us-ashburn-1
         export KUBECONFIG=$HOME/.kube/config
         kubectl create secret docker-registry ocirsecret --docker-server=iad.ocir.io --docker-username='myaccount/ociuser' --docker-password='a<{:1{(B[Zb>2;cD321z' --docker-         email='ociuser@example.com'
         kubectl create -f nodejs_micro.yml
         sleep 30
         kubectl get services nodejsmicroappocir-k8s-service
         kubectl describe pods
         
         
11. Run the Node.js App 


12. Delete the Kubernetes Objects from OKE 

![image](https://user-images.githubusercontent.com/42166489/107638506-af2fbb80-6c95-11eb-9ca5-eae18381a104.png)


     
