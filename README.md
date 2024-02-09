# Kubernetes_Security_
![alt text](https://tse4.mm.bing.net/th?id=OIP.hh0FCRTE-yWIGGs6p1odlgHaEc&pid=Api&P=0&h=220)

### **Securing kubernetes cluster is very important**
Getting access from k8s platform to the underlying operating system.


Security as a spectrum

- Security is combination of Multiple things in multiple levels

***Levels*** -
- Underlying infra
- Kubernetes platform 
- Applications
---------------------------------
Attackers just need one security weak link 
    1. We need to defend each point
 -  Network
 -    Host
 -    Runtime 
----------------------------------------------
With multiple mechanisms 
- Programming best practise- don’t repeat  same logic
- Security best practise - security should be redundant
----------------------------------------------------

![alt text](https://hosting.analythium.io/content/images/size/w2400/2021/04/architecture.png)


   # **1. To build secure images.**

    Each  docker image have different steps 
- Installing tools
- Create users
- Create files
- Run the applications
What  issues can be in image building process?
- Code from untrusted registries
- Vulnerabilities in tools of OS or libraries.
- We need to go through whole code to varify if any unncessary packages and code is present there and eliminate dependencies.
- Use leaner base images 

----------------------------------------------------
What if we build image with vulnerability
- Attacker use vulnerability in the image to break out of the container and get access to host.
- From the host they can access all the other working- ntainers.
- They can read data in the host volumes 
- They can read from the file system 
- They can read kubelet configuration , including kubelet tokens and certificates.
- If attacker reaches the api server than it can further damage the kubernetes cluster.
-------------------------------------------------------------------

What should we do  to prevent hackers
--> Image Scanning to build secure images 

We have tools out there to build secure images 
- Snyk
- Sysdig 
- We need to constantly scan for vulnerability.
How to do image scanning.
- You can scan the image before pushing it to the registry in CI/CD pipeline 


### ***Once the image is build and pass through scanning , then it passes to further process.***

    - But if vulnerability is scanned , gets updated.
    - Scan image regularly in the registry.


# **2. Avoid using Root User**
![alt text](https://1.bp.blogspot.com/-2wNuX_rEkd8/WPN650mSz3I/AAAAAAAAAbI/ILYXBJI369g8L049nAqdAt_nvYI1zW29ACLcB/s1600/root.jpg)
   
- As we know that uid is 0
- Root user have all privileges 
- If attacker get the access of root user , he then easily break out to the container and can access the host

--------------------------
    - Best practice - run container as non-root
    - Create a dedicated user and group

 
***Run the application with that dedicated user instead of root user.***
--------------------------------------
- Avoid using and running  privileges containers
- This way attacker will not  get access to the root user first 
- Then it won't break out and access host

    -----------------------------------

 # **3. Users and Permissions**
    
 ![alt text](https://documents.trendmicro.com/images/TEx/articles/how-users-are-related-to-roles-via-the-RoleBindings.jpg)
    
    - Authentication - who can access the cluster?
    - Authorization - What permissions do they have?
-------------------------------------------------------------------------
    What can the attackers do?
    What permissions do they have?
-------------------------------------------------
### That’s why we need to manage the users and permissions 
- Keep privileges as restrictive as possible!
- Kubernetes resources must be created in namespace and , users must be given read-only  permission in that namespace only.
----------------------------------------------------------------
How to manage permissions in k8s
- We have RBAC  (Role based access control)
- K8s resources that allows you to regulate access based on roles.

------------------------------------------------------------------------------
## In k8s some resources are namespaced and some are cluster scoped

![alt text](https://www.styra.com/wp-content/uploads/2022/10/styra_resource_blog_what-is-rbac.png)

   ## **a. Role**

- That allows viewing , creating and updating deployments.
- That allows viewing , creating and updating services.
- That allows viewing , creating and updating ConfigMaps.



    - Role always sets permissions within a particular namespace.
    - Role defines what can we do with which resources.
    - Role needs to be attached to users/groups.
    - Kubernetes doesn’t manage Users natively.
    - No Kubernetes Objects exist - for representing  normal user accounts.
    - Admins can choose from different authentication strategies.
    - Client certificates are generated for those who are working on databases and apps.
    - And those certificates are registered in Kubernetes cluster for those particular users.
    - Then attach those roles with that respective users - termed as role binding.
------------------------------------------------------------------------
## You also have administrator that update resources in multiple namespaces.
And do cluster wide operations.

## For that we have **ClusterRole**
- It works on whole cluster , instead of particular namespace. 
- Kubernetes administrators can get cluster roles associated to users , that allow them to create view and delete different resources such as nodes, namespaces and volumes.
----------------------------------------------------------------------------------
![alt text](https://anote.dev/wp-content/uploads/2021/01/Kubernetes2-Page-15.jpg)

- We also have non-human user.
- If you are deploying k8s cluster through jenkins pipeline.
- Or running third party service like Istio or Prometheus
- You need to give those tools access to the cluster as well.

- Each pod in k8s gets service account just like human user have roles associate to it.
- Service users use tokens to authenticate to api server.

-------------------------------------------------
## **As security best practice , use RBAC** 

-----------------------------




# **4. Use network Policies** 
    Using Rbac will manage the permissions of external users/apps
    Define what they can perform within the cluster.
    
    ----------------
Communication between pods 
- By default : each pod can talk to other pod.
- If a attacker get access to one pod , they can get access to other pods as well.
- Now in reality every pod don’t need to talk to other pods.
    
    # Sooo,

- We limit the communication between pods 
- Implement network rules.
- We control traffic flow at the IP address or port level, and specify which pod can talk to which pods.
- And also from which pods these can receive traffic from.
------------------------------
## We use NetworkPolicy

    Using network policies we can define some sort of rules.
    Like frontend pod can only talk to backend pod , but cannot talk to database pod.
    And database pod can receive traffic from backend pod only.
-  For max security , you can apply least access allowed rules
-  Define each and every communication rule b/w all pods.
-  This way if attacker get access to one of the pods, they cannot get access of other containers.
--------------------------------------------------

## What Network Policy can do 
- Configuration on a network level using network mesh
- Service mesh is used to do configuration on a service level. (Istio)
- Istio uses proxy in each appli pod to control the traffic coming in to that application and going out of the application.
- With the help of this , we can define stricter communication rules.


--------------------------------------------------------

![alt text](https://trstringer.com/images/why-mtls1.png)
# **5. Encrypt Communication**
## mTLS between Services 
![alt text](https://trstringer.com/images/why-mtls2.png)
- By default communication btw pods is unencrypted, basically in text.
- Attacker could read communication between pods in plain text.
- You can enable mTLS  between pods , so all the communication between pods are encrypted.

- After implementing RBAC , Network Policies and mTLS would be a better practise.

-----------------------------------------------------------




# **6. Secure Secret Data**

    - By default , secret is  not secure.
    - Secrets are used to store sensitive data.
    - Like credentials, secret tokens , private keys
    - Only base64 encrypted data can be decoded
------------------------------------------------------------------------
![alt text](https://www.padok.fr/hubfs/Images/Blog/kubernetes-secret-management-process.png)
## **Use kubernetes own solution**
- Enable encryption using EncryptionConfiguration resource
- But you still have to manage the encryption key 
- You can use 3rd party tools like AWS KMS
- You can use 3rd party secret management solution  , like Vault to store secret more secure.
    

-----------------------------------



# **7. Secure ETCD**
- Secrets and all other configurations data are stored in etcd.
- Kubernetes backing store for all cluster data.
- Every single updata saved in etcd
    
    --------------------------
## If any hacker get access to etcd, it can bypass the API server.

    
    
    Different  ways to secure and run etcd.
    
    - Put etcd behind a firewall
    - Encrypt etcd data 

--------------------

# **8. Protect Your Application data** 
- Etcd store cluster configuration data
- K8s manifest files for k8s resources
    
    -------------
-  But we also have application data 
- Like data stored by database services 
    
- And the biggest damage for any company are related to data 
- Like it may be credit card information
    - Medical records
    - Other private data
    
### Attacker can delete or corrupt your data 
    
### They take your data and demand money for you data.
    
-----------------------
-  You can use **Kasten by Veeam** - kubernetes native data management platform
           
## You can 
    - Regularly back up the data
    - Stores backup safely
    - Be able to easily restore cluster
    - Data is encrypted both during transit and at rest

## Best practisce can be automated backup and restore
    - Attackers can corrupt your backups as well.
    - So attacker ask money for those backups as well.
-----------------------

## You can use **k10** platform  ,  so you can have immutable backups.
    - Ensures that backed-up content cannot be altered  or deleted.
    - Providing an additional layer of protection.

---------------------------


# **9. Configure security policies**

- Know and apply all security best practices
- If your team members don’t know how to secure each and every thong of k8s cluster .
- You will have to check each configuration to make sure everything is secure, manually.
---------------------------------------------------

## So you can define policies to enforce specific configurations.

## With security policies
- Don’t allow pods that run containers with root.
- Network policy need to be deployed for every pod.
- You can use third party tools like open policy agents and kyverno.
--------------

-------------------------

# **10. Last line of defence**
 - You cannot protect  your cluster 100%
 - Attacker just need 1 weak spot
- **You must have a proper strategy and mechanism for disaster recovery.**
 - To Restore your cluster with backup data .
 - Runs Within a short time 
 - Make your application available to your users with minimal affect on user experience.


 -------
 ### For that you need to a tool that allows you to recover te cluster in the same state.
 ------------------
 ### K10 Plateform , with automated disaster recovery.
 
 ### Can reduce the recovery time significantly.

 ![alt text](https://www.baculasystems.com/wp-content/uploads/2019/11/Kubernetes-01.jpg)
 ----------------
 Instead of manually performing disaster recovery.
 You can use these tools.

 And execute an automated , well tested recovery plan.
 Which eventually

    - Minimize effect of the attack
    - Get your application up and running as fast as possible.
    - K10 allows your cluster recovery to any environment.
 
----------------------------










