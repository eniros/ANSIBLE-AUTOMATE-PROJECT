# ANSIBLE-AUTOMATE-PROJECT
ANSIBLE FOR CI-CD

I'll be using the server used for Jenkins for this project

<img width="785" alt="Screenshot 2022-12-02 at 19 51 30" src="https://user-images.githubusercontent.com/61475969/205374781-b93c3e41-a279-41ce-b010-f2d83cb2a275.png">

STEP 1 -Install Ansible on the server by running the command;
```Sudo apt-get update```
```Sudo apt-get install ansible```

Check Ansible is installed by running the command;
```Ansible --service```

<img width="565" alt="Screenshot 2022-12-02 at 19 59 45" src="https://user-images.githubusercontent.com/61475969/205375982-1e19a6a3-4ae3-42b7-abd9-368b309456be.png">


Create a new repository called 'ansible-config-mgt' on github and set up webhooks on it.
https://<jenkins_url:port/github-webhooks>

Create a freestyle project in Jenkins called 'Ansible' , Add the config Repo url to as 'Repositry URL' and configure automatic builds when a trigger is made on the ansible-config-mgt directory via GITScm polling.

Note- specify git branch as main in during configuration and Choose 'Archive this Artefact' as post build action

<img width="882" alt="Screenshot 2022-12-02 at 20 18 07" src="https://user-images.githubusercontent.com/61475969/205378769-e00638a7-c2b5-44d9-9da0-ae3f22680992.png">
 
 Once config is complete, click 'Build' on your 'Ansible' project dashboard page to test connection to your git repo
 
 <img width="882" alt="Screenshot 2022-12-02 at 20 28 27" src="https://user-images.githubusercontent.com/61475969/205380511-943f374b-f9c0-4630-a9c5-e9d250ddfe39.png">
 
 Test configuration by updating a README file on github, then click on the latest build for the console view

<img width="1101" alt="Screenshot 2022-12-02 at 20 32 20" src="https://user-images.githubusercontent.com/61475969/205380880-97c2f630-3a20-484a-a6af-68f0118355d7.png">

STEP 2 - Launch Visual Studio Code on your system and clone your config repo to your system

<img width="1101" alt="Screenshot 2022-12-02 at 20 36 02" src="https://user-images.githubusercontent.com/61475969/205381413-1ff0a06f-541f-4365-8798-a74109c77b07.png">

Run a zsh terminal to create a new branch for development

Create a playbooks directory for storing playbooks
Create an inventory directory for storing inventory files

<img width="912" alt="Screenshot 2022-12-02 at 21 00 20" src="https://user-images.githubusercontent.com/61475969/205385390-46f18e52-2733-4956-8ed4-40f70ec68250.png">

In the playbooks folder, create a common.yml file
In the inventory folder, create dev.yml, prod.yml, staging.yml and uat.yml for dev, prod, staging and uat environments respectively

<img width="910" alt="Screenshot 2022-12-02 at 21 05 43" src="https://user-images.githubusercontent.com/61475969/205386020-26da72f7-242c-42a1-abdb-833e08ac7e47.png">

STEP 3 - Setting Up  Ansible Inventory

we create inventories to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

We need to ssh into our target servers defined in the /inventory/dev.yaml

using SSH-Agent to upload our ssh public key to the jenkins-ansible server

Exit your Jenkins/Ansible server and run the command in the folder your keypair is stored in:

```eval 'ssh-agent -s` ```
```ssh-add privatekey.pem```
Confirm the key has been added with the command below, you should see the name of your key

```ssh-add -l```
Now, ssh into your Jenkins-Ansible server using ssh-agent

```ssh -A ubuntu@public-ip```

<img width="510" alt="Screenshot 2022-12-02 at 21 19 11" src="https://user-images.githubusercontent.com/61475969/205388298-495f0c6c-891a-4ff0-a6a4-fe9d5747586c.png">

Create an instance launch template in AWS, then launch 5 ec2 instances from the template for server roles (NFS, 2WEB SERVERS, DB SERVER, LOAD BALANCER)

<img width="1175" alt="Screenshot 2022-12-03 at 00 43 54" src="https://user-images.githubusercontent.com/61475969/205413998-6c7f360b-f4c9-4bb0-8cf7-0a5d4f93c38f.png">

Copy the private IP ADDRESSES and test that you can take ssh to each by running the command:
```ssh ubuntu@172.31.93.17```
<img width="561" alt="Screenshot 2022-12-03 at 00 55 14" src="https://user-images.githubusercontent.com/61475969/205414397-31ebe295-c563-4c52-8f77-dafc00b3c449.png">


update /inventory/dev.yaml with server details

````
[nfs]
172.31.95.182 ansible_ssh_user='ubuntu'

[webservers]
172.31.87.250 ansible_ssh_user='ubuntu'
172.31.93.17 ansible_ssh_user='ubuntu'

[db]
172.31.83.88 ansible_ssh_user='ubuntu' 

[lb]
172.31.27.213 ansible_ssh_user='ubuntu'
````

 
 
Step 4 -Creating a Common Playbook


Update code in /playbooks/common.yaml

```
 ---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```        
<img width="1155" alt="Screenshot 2022-12-02 at 21 35 17" src="https://user-images.githubusercontent.com/61475969/205392989-aa315c3b-3a59-4195-8bce-80677cb5b889.png">
        
Commit the changes in the files to github by running the command:
```git add.``` then ```git commit -m "made some changes"```
<img width="1064" alt="Screenshot 2022-12-03 at 01 18 46" src="https://user-images.githubusercontent.com/61475969/205415686-5c3d2e74-30b0-41cf-99db-ee21ccceebcc.png">

<img width="1068" alt="Screenshot 2022-12-03 at 01 22 00" src="https://user-images.githubusercontent.com/61475969/205415693-f9789591-c67d-4689-a6ed-c72b7932c697.png">

<img width="1062" alt="Screenshot 2022-12-03 at 01 25 25" src="https://user-images.githubusercontent.com/61475969/205415816-37ca844d-6622-4996-b5ad-5f00f3c362ab.png">

 
Next push code into repository and create a pull request to the main branch. Jenkins checksout the code and builds an artifact that is published on the ansible server.

<img width="1041" alt="Screenshot 2022-12-03 at 01 31 52" src="https://user-images.githubusercontent.com/61475969/205416122-ebc9be6d-9f4f-4b6e-afa9-895671528fc4.png">

Run the build command in Jenkins, then check the console for the latest update message

<img width="769" alt="Screenshot 2022-12-03 at 02 26 53" src="https://user-images.githubusercontent.com/61475969/205418117-0b72a240-e0cc-429f-a826-47ab98bb92e9.png">

<img width="879" alt="Screenshot 2022-12-03 at 02 17 30" src="https://user-images.githubusercontent.com/61475969/205418037-65708c2e-7c58-4fd0-a2e3-eaa9f9e8275e.png">






Step 5 -Run First Ansible TEST

SSH into the ansible server , then run the command;

```
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<ansiblelatestbuildnumber>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<ansiblelatestbuildnumber>/archive/playbooks/common.yml
 ```

 
 <img width="1311" alt="Screenshot 2022-12-04 at 16 12 13" src="https://user-images.githubusercontent.com/61475969/205502205-f9da309a-3fd3-464b-bd42-9a4d39fc3647.png">

Check the server to confirm the changes are reflected. Check if wireshark is installed

<img width="572" alt="Screenshot 2022-12-04 at 16 22 22" src="https://user-images.githubusercontent.com/61475969/205502696-4f348225-1b7f-492e-8792-3c92a9cb2ab2.png">

End





