Use a SSH Jump Host With Ansible

In this post we will demonstrate how to use a SSH Bastion or Jump Host with Ansible to reach the target server.
In some scenarios, the target server might be in a private range which is only accessible via a bastion host, and that counts the same for ansible as ansible is using SSH to reach to the target servers.
