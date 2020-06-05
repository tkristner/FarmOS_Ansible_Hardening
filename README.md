# Ansible_FarmOS
Full stack deployment and security hardening over Debian 9 for FarmOS. With these ansible playbooks running over a fresh Debian 9 install, you'll got a ready to be configured FarmOS installed. Because it's based on Drupal, and it's a common target to exploit, I choose to put an additional front Nginx auth to avoid unauthorized access.

Please bear with me if there are any mistakes.

Do not hesitate to make pull requests.


## What will happen with these playbooks:

- Configure Debian 9 with a new user, adding SSH keys, removing root over ssh, installing Inspec etc..
- Doing the dev-sec hardening of SSH (the Inspec baseline is also installed)
- Doing the dev-sec hardening of Linux (the Inspec baseline is also installed)
- Installing Fail2ban and configure it for SSH
- Installing Nginx and configure a temp web server for Let's Encrypt challenge
- Doing the Let's Encrypt HTTP challenge to issue your certificates
- Configure Nginx for your HTTPS final FarmOS website
- Doing the dev-sec hardening of Nginx (the Inspec baseline is also installed)
- Installing the Nginx WAF called Nemesida Free
- Installing PHP
- Installing MySQL
- Doing the dev-sec hardening of MySQL (the Inspec baseline is also installed)
- Installing FarmOS


## What you should do:

***All the command we run below are executed on the Ansible host, not the server where we install FarmOS, because it's better to keep things apart ansible is not installed on the server***

- Fresh Debian 9 install in some server/vps, with python3 installed to allow ansible to run tasks on it.

- A host on which you'll install and run Ansible and associated roles, like a vm or Raspberry Pi or whatever:
  - ``apt-get install ansible``
  
- Configure your /etc/ansible/hosts to add your Farmos Debian 9 server IP ( https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html )

- Install the Ansible roles we'll use these commands:
  - ``ansible-galaxy install dev-sec.os-hardening``
  - ``ansible-galaxy install dev-sec.ssh-hardening``
  - ``ansible-galaxy install dev-sec.nginx-hardening``
  - ``ansible-galaxy install dev-sec.mysql-hardening``
  - ``ansible-galaxy install nginxinc.nginx``
  
- Define the variables located in vars/default.yml

- Rename the conf file inside the files directory and define <variables> that are inside ( it's the HTTPS Nginx server definition file that we upload near the end of the deployment)

- Generate SSH keys and please with a good passphrase:
  - ``ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519``
 
- Run the ansible playbook A_Linux_pre-staging.yml with root user and your ssh password (this will be removed after, allowing only non-root user and only with ssh keys)
  - ``ansible-playbook A_Linux_pre-staging.yml -u root --ask-pass``

- Run the ansible playbook B_Farmos_full_staging.yml with the user and ssh keys you define earlier in variables:
  - ``ansible-playbook B_Farmos_full_staging.yml -u <your-username> --key-file=~/.ssh/id_ed25519``

- If everything run smoothly please go to your new website https://<www.domain.com>/ to start the Drupal/FarmOS configuration, please be sure to use the variables value you defined earlier

- Enjoy !


***You can run the following inspec command to check your compliance with installed security baseline :***

- ``inspec exec ssh-baseline``
- ``inspec exec linux-baseline``
- ``inspec exec nginx-baseline``
- ``inspec exec mysql-baseline``

Please read carefully the output because some error are normal and compliant, like return:'no','no' expected:'no' because of some definitions in config files. 