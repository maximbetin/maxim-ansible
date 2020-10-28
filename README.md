# Propagate SSH keys to GCE VMs

Using a pre-existing SSH key (through [project-wide GCE metadata](https://cloud.google.com/compute/docs/instances/adding-removing-ssh-keys#project-wide) or other means), Ansible will use the default public SSH key located in the Control Node's `/home/maxim/.ssh` directory to communicate with the fleet of instances.

- Create the user that we want to be replicated to the fleet, named `devops`:
  ```
  sudo -i
  useradd -m -s /bin/bash devops
  passwd devops
  echo -e ‘devops\tALL=(ALL)\tNOPASSWD:\tALL’ > /etc/sudoers.d/devops
  ```
- Login as the user:

  `su devops`

- Create an SSH key for this user that will be sent out to the nodes:

  `ssh-keygen`

  In my case, I've stored the public and private SSH keys in the `/ssh-keys` directory. The content is redacted.

- Edit the inventory file `hosts` with the IPs of the nodes:
  ```
  [servers]
  34.124.178.131
  34.68.31.22
  34.69.222.232
  ```
  Replace those IPs appropriately.

- Run the Ansible playbook:

   `ansible-playbook add-user-ssh.yml -i hosts`

   It will propagate the SSH key under `ssh-keys/id_rsa.pub` to the hosts specified. It will also create that user in the nodes, add it to sudoers, disable password authentication and root login for the user. These are optional. Once done, verify the connection over SSH can be established from the Control Node with `sudo -i ssh-keys/id_rsa.pub devops@34.124.178.131` i.e.
