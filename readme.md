# This hands on designed for
# creating linux user accounts and setting up ssh keys using ansible

# we will be using a contabo linux instance with ubuntu 20.04 operating system as control node

# ansible is an open-source IT automation tool. we can use ansible to configure systems, deploy software, and orchestrate more advanced IT tasks such as continuous deployments.
# we use declarative syntax for utilising ansible's powerful yet simple functionality. we call these .yaml (or .yml) files playbook. each playbook may have a single task or may contain number of tasks.

# during below excercise we shall show incremental increase in functionality on our playbook by adding one task at each step so that the logic behind developing playbook syntax can easily be appreciated at the end of this session




1- installing ansible on control node
# make remote connection to the control node
$ ssh <user>@<ip address>

# once connected you may want to name the instance so that you can easily pick the instance in multi instance environment
$ sudo hostnamectl set-hostname control-node

# our control node's operating system is ubuntu linux therefore we shall be using apt distro for updating system and installing extras
$ sudo apt-get update -y
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible

# check ansible version
$ ansible --version
# you should be able to see ansible 2.9.6 or newer version




2- creating new ssh asymetric key pair on control node

# key pair will not be required immediately during this excercise however if you have not done the same before you can use the same commands to create one on your local machine where you will be connecting to control node.
# go into .ssh folder
$ cd .ssh/

# when you check content of the .ssh folder
$ ls -al
# you should see similar to folowing list
drwx------  2 root root 4096 Aug 17 12:36 .
drwx------ 22 root root 4096 Aug 16 17:56 ..
-rw-r--r--  1 root root 6438 Apr 26 11:42 known_hosts

# create new asymetric key pair
$ ssh-keygen

# when prompted enter the file name and if you want to set up password for the key pair enter when prompted otherwise click enter twice for no passphrase
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): /root/.ssh/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:uU2MkZKOLDYGPVJIGpyHeY3Tt/3oknfD7yBrS8jfotw devsecops@control-node
The key's randomart image is:
+---[RSA 3072]----+
| ..=oOB.         |
|  =oB+==..       |
|  .o o+o+o       |
|       ..=.      |
|        S oo     |
|       . =. .    |
|        +o+++    |
|       .o+=++.   |
|        o=E+.+o  |
+----[SHA256]-----+

# now check the content of .ssh folder and you should see screen similar to the following
devsecops@control-node:~/.ssh$ ls -al
total 24
drwx------  2 root root 4096 Aug 17 12:36 .
drwx------ 22 root root 4096 Aug 16 17:56 ..
-rw-------  1 root root 2602 Aug 17 12:36 id_rsa
-rw-r--r--  1 root root  569 Aug 17 12:36 id_rsa.pub
-rw-r--r--  1 root root 6438 Apr 26 11:42 known_hosts

# use cat command to check content of private and public key pair files
$ cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjESSSAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAuR6oMqWHQFBN2lMPDDJ8iEfTz836SItZZhycCYQFZpTAMLWJuhp9
Pov2PPoFWB8rE3ZjwOlkfzX7/nWjbc8eD0Yc4gFmkykjq6U/iqtCq4LipTnGfS8BZWQNpM
0+TEaImpH58eSgrtMeqThQ36ZtJplD5CwHStokcU2r2hOrbFSBtWYXmOcuTujHMDe2jCKg
ASdfresd86754e3cbjJHTFGU8ytgb%$Ehjki99jjkklllLLLLuTGbnuhy3c8ygnbqxLL/g
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
-----END OPENSSH PRIVATE KEY-----

$ cat id_rsa.pub
ssh-rsa SSSAB3NzaC1yc2EAAAADAQABSSSBgQC5HqgypYdAUE3aUw8MMnyIR9PPzfpIi1lmHJwJhAVmlMAwtYm6Gn0+i/Y8+gVYHysTdmPA6WR/Nfv+daNtzx4PRhziAWaTKSOrpT+Kq0KrguKlOcZ9LwFlZA2kzT5MRoiakfnx5KCu0x6pOFDfpm0mmUPkLAdK2iRxTavaE6tsVIG1ZheY5y5O6McwN7aMIqBgMwZVRNBIsMHtkaJPUsaZzuc5cPLRhSKWTxs1x3t5lbxf3PfEVrtOndghHGRTG45jaLK//vbthsiyher8ubchcd8jhfbiKBNTHY67ujsgf/thrdsk+rZjqBlp7loNs8b9VYE5Ip77jSISfhMhY80kGHh2cLulKN1aW9JPmwL6/gYlYKC1pNsZAktOqyhHMoZkfn47DD+125lmbz9t2dBpOxYBg0tTqhvCnJrHeKHzG9+7/K3bq9cAcsE1KAXrp8rBa2FedZOz91yQFpt1n/2BVZyvowf8O3QS81WgwWM= devsecops@control-node





3- Ansible documentation says: Ansible Playbooks offer a repeatable, re-usable, simple configuration management and multi-machine deployment system, one that is well suited to deploying complex applications. If you need to execute a task with Ansible more than once, write a playbook and put it under source control. Then you can use the playbook to push out new configuration or confirm the configuration of remote systems.

# first stage (aim of this hands on) is to type playbooks, run playbooks and see that desired task executed and check that our instance actually reached the desired state.

# we will be working on placing our sysntax in source control repositories (bitbucket) and integrating our playbooks to deployment automation tools (jenkins) and run the tasks as ci/cd pipeline jobs in an automated, simple and easily repeatable way at following stages. 

# we are starting make use of first stage of playbook. control node is central to our plan. we shall add users for each member of the devsecops team so that they can use control node to inflict changes on other resources. 

# create folder named ansible to keep files together and go into the folder
$ mkdir ansible
$ cd ansible

# check list of control node's current users
$ getent passwd
# check list of control node's current groups and users
$ getent group

# we start typing a yaml file. our yaml file to contain basic action description and list of users as variables
$ vim control-node-users.yaml

---
- hosts: "localhost"
  connection: "local"
  vars:
    users:
    - hcokakog
    - fsari
    - maltun
    - msahin
    - serdem
  tasks:
  - name: "Create user accounts and add users to groups"
    user:
      name: "{{ item }}"
      groups: "devops"
    with_items: "{{ users }}"

# you should have noticed that "with_item" used for creating a loop. with_item is widely used in ansible playbooks.

# create a sub folder under ansible named files and paste each persons' public key content into each person's file. for easy reference you can use <username.key.pub> name format.
$ mkdir files
$ cd files
$ touch "<content of id_rsa.pub>" maltun.key.pub
# repeat the above process for each user and content of your ansible folder should look like:
- ansible
 - files
    fsari.key.pub
    hcokakog.key.pub
    maltun.key.pub
    msahin.key.puk
    serdem.key.pub
 ansible.cfg
 readme.md
 users.yaml

# run your first ansible playbook to add users to control node so that each team member can connect to control node without needing a password and make use of pre recorded ansible playbooks as and when needed from control node so that every one can use the same playbooks centrally located to make sure all done in a uniform manner. (once all the tasks, variables and playbook syntax fully matured and tested here, those final versions will later be transferred to bitbucket and will be called by a jenkins job so that functions of playbooks can be carried out harmoniously with other microservices)
$ cd ..
$ ansible-playbook control-node-users.yaml

# you should see similar to following screen
devsecops@control-node:~/ansible$ ansible-playbook control-node-users.yaml

PLAY [localhost] ****************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************
ok: [localhost]

TASK [Create user accounts and add users to groups] ******************************************************************************
changed: [localhost] => (item=hcokakog)
changed: [localhost] => (item=fsari)
changed: [localhost] => (item=maltun)
changed: [localhost] => (item=msahin)
changed: [localhost] => (item=serdem)

PLAY RECAP ********************************************************************************************************
localhost     : ok=5    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

# now we have created five users under devops group however we have needed to manually take id_rsa.pub file content for each user from their local machine and pasted each one to their <user>.key.pub files under folder named files. for sake of best practise we could add ssh keys with another task. will try doing that with the next playbook




4- we will update our playbook in order to automate adding ssh keys. 
# ansible executes content of playbook from top to bottom. we can define more than one task in the same playbook. therefore we will add another task at the end of existing control-node-users.yaml file for ssh keys.

$ vim control-node-users1.yaml

---
- hosts: "localhost"
  connection: "local"
  vars:
    users:
    - hcokakog
    - fsari
    - maltun
    - msahin
    - serdem
  tasks:
  - name: "create user accounts and add users to groups"
    user:
      name: "{{ item }}"
      groups: "devops"
    with_items: "{{ users }}"
  - name: "add authorized keys"
    authorized_key:
      user: "{{ item }}"
      key: "{{ lookup('file', 'files/'+ item + '.key.pub') }}"
    with_items: "{{ users }}"

# you may delete users each time in order to get yellow coloured "changed" text. if you do not delete users before each and every time run the new / updated playbook you will get green text for the ones unchanged, yellow text for the ones updated / changed and red coloured text for the ones failed.
# when you run above playbook with below command you will get green screen as we have not deleted any users previously

$ ansible-playbook control-node-users1.yaml

# and you should see screen similar to:
devsecops@control-node:~/ansible$ ansible-playbook control-node-users1.yaml
PLAY [localhost] ********************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************
ok: [localhost]

TASK [create user accounts and add users to groups] ****************************************************************************
ok: [localhost] => (item=hcokakog)
ok: [localhost] => (item=fsari)
ok: [localhost] => (item=maltun)
ok: [localhost] => (item=msahin)
ok: [localhost] => (item=serdem)

TASK [add authorized keys] *****************************************************************************************************
ok: [localhost] => (item=hcokakog)
ok: [localhost] => (item=fsari)
ok: [localhost] => (item=maltun)
ok: [localhost] => (item=msahin)
ok: [localhost] => (item=serdem)

PLAY RECAP *****************************************************************************************************
localhost     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

# when you want to manage more than one group for some users and you want to formulise the same you can also use slightly improved version of variables section for above playbook. we will name the improved one control-node-users2.yaml

$ vim control-node-users2.yaml

---
- hosts: "localhost"
  connection: "local"
  vars:
    users:
    - username: "hcokakog"
      groups: "devops"
    - username: "fsari"
      groups: "devops"
    - username: "maltun"
      groups: "devops"
    - username: "msahin"
      groups: "devops"
    - username: "serdem"
      groups: "devops"
  tasks:
  - name: "create user accounts and add users to groups"
    user:
      name: "{{ item.username }}"
      groups: "{{ item.groups }}"
    with_items: "{{ users }}"
  - name: "add authorized keys"
    authorized_key:
      user: "{{ item.username }}"
      key: "{{ lookup('file', 'files/' + item.username + '.key.pub') }}"
    with_items: "{{ users }}"

# when you run above playbook with below command you will get green coloured text on the screen as we have not deleted existing users 
$ ansible-playbook control-node-users2.yaml

# and you should see the screen similar to:
devsecops@control-node:~/ansible$ ansible-playbook control-node-users2.yaml

PLAY [localhost] ********************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [localhost]

TASK [create user accounts and add users to groups] ****************************************************************************
ok: [localhost] => (item={'username': 'hcokakog', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'fsari', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'maltun', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'msahin', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'serdem', 'groups': 'devops'})

TASK [add authorized keys] *****************************************************************************************************
ok: [localhost] => (item={'username': 'hcokakog', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'fsari', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'maltun', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'msahin', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'serdem', 'groups': 'devops'})

PLAY RECAP ********************************************************************************************************
localhost   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0




5- so far we have learned adding users. but people come and go. (no one is indispensible.) they may change groups, they may go to join another company and new recruits may join to replace them therefore ideally we have to be able to get our playbook to remove users as well as adding them.

# we shall now update script of our playbook to simultaneously add and remove users. we will name the new playbook control-node-users3.yaml

$ vim control-node-users3.yaml

---
- hosts: "localhost"
  connection: "local"
  vars:
    users:
    - username: "hcokakog"
      groups: "devops"
    - username: "fsari"
      groups: "devops"
    - username: "serdem"
      groups: "devops"
    remove_users:
    - "msahin"
    - "maltun"
  tasks:
  - name: "create user accounts & add them to groups"
    user:
      name: "{{ item.username }}"
      groups: "{{ item.groups }}"
    with_items: "{{ users }}"
  - name: "remove old users"
    user:
      name: "{{ item }}"
      state: "absent"
    with_items: "{{ remove_users }}"
  - name: "add authorized keys"
    authorized_key:
      user: "{{ item.username }}"
      key: "{{ lookup('file', 'files/' + item.username + '.key.pub') }}"
    with_items: "{{ users }}"

# when you run above playbook with below command you will get green coloured text for three users on the screen as we have not deleted three of the existing users and we will get yellow coloured text for two users as we are deleting them this time.
$ ansible-playbook control-node-users3.yaml


# and you should see a screen similar to:
devsecops@control-node:~/ansible$ ansible-playbook control-node-users3.yaml

PLAY [localhost] *********************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [localhost]

TASK [create user accounts & add them to groups] *******************************************************************************
ok: [localhost] => (item={'username': 'hcokakog', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'fsari', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'serdem', 'groups': 'devops'})

TASK [remove old users] ********************************************************************************************************
changed: [localhost] => (item=msahin)
changed: [localhost] => (item=maltun)

TASK [add authorized keys] *****************************************************************************************************
ok: [localhost] => (item={'username': 'hcokakog', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'fsari', 'groups': 'devops'})
ok: [localhost] => (item={'username': 'serdem', 'groups': 'devops'})

PLAY RECAP *********************************************************************************************************
localhost       : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 

# if you want to check groups and users you can use
$ getent group
# command from command line (terminal) at any time


