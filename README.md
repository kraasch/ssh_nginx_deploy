
# Deploy script for local web project to Nginx server via SSH.

This script automatically creates an Nginx configuration on the remote server.
It manipulates directotries like

 - `/etc/nginx/sites-available/APP.config`,
 - `/etc/nginx/sites-enabled/APP.config` and
 - `/var/www/APP`.

**Note**: if configured correctly this script deploys automatically to the remote server.
If configured wrong it might create artifacts in the before-mentioned directories prohibiting nginx from starting.
If so, please clean up these directories manually.

**Warning**: this script will automatically remove data on the remote server according to the values provided (see section `remove-clean-all` in the Makefile).
Make sure to have a copy and a backup of your valuable data.

## Instructions

Basic checklist:

 - [ ] Create ssh alias in `~/.ssh/config`.
 - [ ] Run ssh-agent (optional but recommended).
 - [ ] Provide a basic shell config file for with the project data.
 - [ ] Install and configure ssh and nginx on remote server.
 - [ ] Run `ssh_deploy` commnand.

### Create an SSH alias

Add an entry of the follwing form to your `~/.ssh/config` file:

```
Host some_ssh_alias
	IdentityFile ~/.ssh/<YOUR_CONFIGURED_PRIVATE_KEY>
	IdentitiesOnly yes
	User root
	hostname <YOUR_IP>
	Port <YOUR_PORT>
```

### Run the SSH agent.

Also consider adding the ssh key to the key chain in order to avoid reyping the local encryption password.
For this make sure the `ssh-agent` is running the `private_key` is added to the agent with the `ssh-add` command:

```
eval `ssh-agent -s`
ssh-add ~/.ssh/private_key
ssh-add -l
```

This means the public keys for **shh** must be configured on the remote server.

If everything is ready to go initially run the command `make setup`.
This will setup a temporary config file `temp_config-file` and a temporary link `temp_webpage` to the web project directory containing the `dist` directory.

### Setup shell config file for local project

Write a file named `ssh_deploy.conf` (can be hidden too) with the following content.

```
#! /bin/bash
webpage_file_dir=example-com
app_name=myweb
domain_name=example.com
ssh_alias=some_ssh_alias
certbot_suffix=_certbot
```

The `webpage_file_dir` and `app_name` are the directories created for the web project on the web server.
The variable `app_name` can be found `/var/www/APP` and the variable `webpage_file_dir` in `/etc/nginx/sites-.../`.
The `domain_name` is part of Nginx's configuration.
The `ssh_alias` must be the same as mentioned before.
The `certbot_suffix` can be empty if cerbot is not used or must otherwise be equal to `_certbot`.

### Run the script

Then run the command `ssh_deploy [<config_file_name>]` to deploy everything according to the configuration to the remote server.

For subsequent deploys -- e.g. after code chagnes in the web project directory -- only run the command `make redeploy`.

If this is the initial deploy consider setting up **certbot** afterwards.

**Note**: this script includes the current config files for ssh, nginx and certbot.
Make sure to keep all config files up to date according to what ever versions you are running.

## Related resources on the web

 - other related topics
   - https://www.digitalocean.com/community/tutorials/install-uninstall-nodejs-ubuntu
   - https://vuetifyjs.com/en/features/theme/
   - [How to Point a Domain Name to an IP Address](https://www.youtube.com/watch?v=QcNBLSSn8Vg), a video that teaches about DNS records.
 - deploy vue on nginx
   - https://jgefroh.medium.com/a-guide-to-using-nginx-for-static-websites-d96a9d034940
   - https://www.digitalocean.com/community/tutorials/how-to-publish-a-vuetify-app-with-nginx-on-ubuntu-20-04
   - https://certbot.eff.org/instructions?ws=nginx&os=debianbuster
 - other
  - https://www.serverlab.ca/tutorials/linux/web-servers-linux/how-to-configure-multiple-domains-with-nginx-on-ubuntu/


