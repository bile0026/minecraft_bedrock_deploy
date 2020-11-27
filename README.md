# minecraft_bedrock_deploy

Sets up a minecraft bedrock server on specified version on linux systems running systemd. I'm assuming you have some knowledge of how to run Ansible playbooks, and won't go into much detail on that. Minecraft server will run as a service and automatically start on boot. Should work on all RedHat and Debian-based operating systems, EXCLUDING Rasberry Pi. So far only tested on CentOS 8, and Ubuntu 20.04. Currently doesn't work on Rasberry Pi, due to ARM CPU architecture. Might work on this down the road, but this requires emulation, which would make it run VERY slow. You're much better off finding an old PC and putting linux on it. For updates, check out my update playbook https://github.com/bile0026/minecraft_bedrock_update.

# Current caveats
* Does not handle opening firewall on system if required
* Assumes SELINUX is disabled on systems that support it. Seems to work with SELINUX set to "enforcing", but needs more testing.
* jinja templates for permissions.json and whitelist.json need some work on formatting. Might need to remove the final comma from the list of objects.

# How to use this playbook

Run the playbook with this command, substituting your credentials. -k is used to prompt for user password, -K is for the sudo/become password. If you want to use a private key switch out -k (lowercase) with --key-file <path>. This playbook requires become as it will install packages and create services.

```
ansible-playbook -i hosts deploy_minecraft.yml -u <username> -k -K
```

Set the vars as required if any are different from defaults. Change these in `minecraft_bedrock_deploy/defaults/main.yml`, or you could setup group_vars/host_vars if you wanted a scaled deployment to multiple servers.

```
# variables for server customization. See readme for allowed options.
minecraft_version: 1.16.100.04
minecraft_server_name: my_server
max_players: 10
gamemode: survival
difficulty: easy
online_mode: "true"
whitelist: "false"
# server port needs to be unique for each server running on the same host
server_port: 19134
level_name: my_level
level_seed:

# server permission variables (list xuids)
operator_accounts:
  - xuid_1
  - xuid_2
members:
  - xuid_1
  - xuid_2
visitors:
  - xuid_1
  - xuid_2

# whitelist variables (list gamertags)
whitelist_accounts:
  - gamertag_1
  - gamertag_2
```
