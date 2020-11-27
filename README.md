# minecraft_bedrock_deploy

Sets up a minecraft bedrock server on specified version on linux systems running systemd. I'm assuming you have some knowledge of how to run Ansible playbooks, and won't go into much detail on that. Minecraft server will run as a service and automatically start on boot. Should work on all RedHat and Debian-based operating systems, EXCLUDING Rasberry Pi. So far only tested on CentOS 8, and Ubuntu 20.04. Currently doesn't work on Rasberry Pi, due to ARM CPU architecture. Might work on this down the road, but this requires emulation, which would make it run VERY slow. You're much better off finding an old PC and putting linux on it. For updates, check out my update playbook https://github.com/bile0026/minecraft_bedrock_update.

# Current caveats
* Does not handle opening firewall on system if required
* Assumes SELINUX is disabled on systems that support it. Seems to work with SELINUX set to "enforcing", but needs more testing.
* Have to specify version manually in the version variable. Check here for latest version: https://www.minecraft.net/en-us/download/server/bedrock I hope to have this automatically grab the latest version in the future.

# How to use this playbook

Run the playbook with this command, substituting your credentials. -k is used to prompt for user password, -K is for the sudo/become password. If you want to use a private key switch out -k (lowercase) with --key-file <path>. This playbook requires become as it will install packages and create services.

```
ansible-playbook -i hosts deploy_minecraft.yml -u <username> -k -K
```

Set the vars as required if any are different from defaults. Change these in `minecraft_bedrock_deploy/defaults/main.yml`, or you could setup group_vars/host_vars if you wanted a scaled deployment to multiple servers.

# Configurable variables
* gamertags are case-sensitive and might contain spaces. Double check these careully if you want to use the whitelist.
* as a preventative measure, whitelist is not automatically enabled if whitelist is created. Set the `whitelist` variable to enable it when you are sure it's correct.
* pay attention to variables that use quote around true/false values. Make sure to preserve the "" or the templates may not work correctly. These are strings, not literal true/false values.

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

# server permission variables set xuids. Add more as needed.
account_permissions:
  - permission: operator
    xuid: xxxxxxx
  - permission: member
    xuid: xxxxxxy
  - permission: visitor
    xuid: xxxxxxz

# whitelist variables set gamertags. Use "" on true/false. Add more as needed
whitelist_accounts:
  - playerlimit: "true"
    gamertag: gamertag1
  - playerlimit: "false"
    gamertag: gamertag2
```
