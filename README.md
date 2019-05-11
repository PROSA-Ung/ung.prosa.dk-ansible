# Ansible playbook and roles for ung.prosa.dk
## Vaulted variables
* vault password can be aquired by asking Jeppe or Dennis.
* the vault password should be placed into a file called `vault.key` in the root of the project (it's added to .gitignore so it won't be comitted by accident)

## How to deploy
You'll need to have SSH root access on the ung.prosa.dk server, again this can be aqquired by talking to Jeppe or Dennis.  
When you have that you need to [install ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) on your machine.

Then you can deploy the playbook by running `ansible-playbook -i inventory playbook.yml` from the root directory.

## How to setup a new Docker based service
1. Create a new file in the `roles/docker/tasks/services.yml` directory named after the service.
2. insert stuff into the file… see the `default.yml` for syntax. Things to note:  
```
env:
    VIRTUAL_HOST: "{{ servicename.domain }}"
    VIRTUAL_PORT: 3000 #can be omitted if the service runs on port 80 by default, or if it only exposes one port.
    LETSENCRYPT_HOST: "{{ servicename.domain }}"
    LETSENCRYPT_EMAIL: "{{ letsencrypt_email }}"
```
These variables tell the Nginx-proxy container where to route it's traffic, and what domains to issue certificates for.  
```
labels:
  com.ouroboros.enable: "true"
```
We have a ouroboros service running that you can activate by adding the above label. it checks every 5 minutes if there's a newer image, if there is it'll pull it and replace the currently running container with the new image.
