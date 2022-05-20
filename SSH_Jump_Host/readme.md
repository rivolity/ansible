
## Using a Bastion with Ansible
Use a SSH Jump Host With Ansible

In this post we will demonstrate how to use a SSH Bastion or Jump Host with Ansible to reach the target server.
**In the example we will try to print the OS_release of the target servers.**
In some scenarios, the target server might be in a private range which is only accessible via a bastion host, and that counts the same for ansible as ansible is using SSH to reach to the target servers.
### Using a Bastion with Ansible
#### 1.Structure
	├── ansible
	│   ├── inventories
	│   │   └── inventory.yml
	│   └── roles
	│   │   ├── get_os
	│   │   │   ├── tasks
	│   │   │   │   └── main.yml
	│   ├── playbook.yml
#### 2.Inventory
Content of inventory.yml
> 

    [devapp]
	dev-cch1 ansible_host=12.12.12.213 ansible_user=onefrontal ansible_ssh_private_key_file=~/.ssh/key_private_host.pem
	dev-cch2 ansible_host=12.12.12.214 ansible_user=onefrontal ansible_ssh_private_key_file=~/.ssh/key_private_host.pem
	dev-cch3 ansible_host=12.12.12.215 ansible_user=onefrontal ansible_ssh_private_key_file=~/.ssh/key_private_host.pem

	[devapp:vars]
	ansible_ssh_common_args='-o StrictHostKeyChecking=no -o ProxyCommand="ssh -i ~/.ssh/key_private_jumper.pem -W %h:%p -q user@jumper-host"'

#### 3.Role
 content of the role "get_os" main.yml
 
	# Get OS version
	- name: Check Distribution Version
	  shell: cat /etc/os-release
	  register: response
	- debug: msg="{{ response.stdout }}"

#### 4.Playbook
 content of the playbook.yml

	- name: Check OS Version
	  hosts: devapp
	  roles:
	    - get_os

#### 5.Execution
	ansible-playbook -i inventories/inventory.yml playbook.yml

#### 6.Results

	ok: [dev-cch1] => {
	    "msg": "NAME=\"CentOS Linux\"\nVERSION=\"7 (Core)\"\nID=\"centos\"\nID_LIKE=\"rhel fedora\"\nVERSION_ID=\"7\"\nPRETTY_NAME=\"CentOS Linux 7 (Core)\"\nANSI_COLOR=\"0;31\"\nCPE_NAME=\"cpe:/o:centos:centos:7\"\nHOME_URL=\"https://www.centos.org/\"\nBUG_REPORT_URL=\"https://bugs.centos.org/\"\n\nCENTOS_MANTISBT_PROJECT=\"CentOS-7\"\nCENTOS_MANTISBT_PROJECT_VERSION=\"7\"\nREDHAT_SUPPORT_PRODUCT=\"centos\"\nREDHAT_SUPPORT_PRODUCT_VERSION=\"7\""
	}
	ok: [dev-cch2] => {
	    "msg": "NAME=\"CentOS Linux\"\nVERSION=\"7 (Core)\"\nID=\"centos\"\nID_LIKE=\"rhel fedora\"\nVERSION_ID=\"7\"\nPRETTY_NAME=\"CentOS Linux 7 (Core)\"\nANSI_COLOR=\"0;31\"\nCPE_NAME=\"cpe:/o:centos:centos:7\"\nHOME_URL=\"https://www.centos.org/\"\nBUG_REPORT_URL=\"https://bugs.centos.org/\"\n\nCENTOS_MANTISBT_PROJECT=\"CentOS-7\"\nCENTOS_MANTISBT_PROJECT_VERSION=\"7\"\nREDHAT_SUPPORT_PRODUCT=\"centos\"\nREDHAT_SUPPORT_PRODUCT_VERSION=\"7\""
	}
	ok: [dev-cch3] => {
	    "msg": "NAME=\"CentOS Linux\"\nVERSION=\"7 (Core)\"\nID=\"centos\"\nID_LIKE=\"rhel fedora\"\nVERSION_ID=\"7\"\nPRETTY_NAME=\"CentOS Linux 7 (Core)\"\nANSI_COLOR=\"0;31\"\nCPE_NAME=\"cpe:/o:centos:centos:7\"\nHOME_URL=\"https://www.centos.org/\"\nBUG_REPORT_URL=\"https://bugs.centos.org/\"\n\nCENTOS_MANTISBT_PROJECT=\"CentOS-7\"\nCENTOS_MANTISBT_PROJECT_VERSION=\"7\"\nREDHAT_SUPPORT_PRODUCT=\"centos\"\nREDHAT_SUPPORT_PRODUCT_VERSION=\"7\""
	}
