# minecraft_bedrock_deploy

Sets up a minecraft bedrock server on specified version on linux systems running systemd. I'm assuming you have some knowledge of how to run Ansible playbooks, and won't go into much detail on that. Minecraft server will run as a service and automatically start on boot. Should work on all RedHat and Debian-based operating systems, EXCLUDING Rasberry Pi. So far only tested on CentOS 8, and Ubuntu 20.04. Currently doesn't work on Rasberry Pi, due to ARM CPU architecture. Might work on this down the road, but this requires emulation, which would make it run VERY slow. You're much better off finding an old PC and putting linux on it. If the specified minecraft service already exists the minecraft_server_update roll will be called and will upgrade the server without any changes to settings. Update role will put a backup of the server into the `backup_folder` prior to pushing new files in.

* Make sure to deploy from a tag, not from master branch. Tags should be stable, master branch should be considered development and may not always be in a stable state.
```
git clone <repo_url>
git checkout tag/<tag_name>
```

# Current Issues
* If updating from any version prior to 1.16.210.05 to a newer version, some of the server.properties files changed regarding the "server-authoritative" options. You'll need to update these manually for now until the upgrade job is updated to handle this. New deployments should not be affected.

# Current caveats
* Currently just disables firewalld on RedHat-based servers. Working on modifying instead.
* Disables SELINUX on systems running it. Working on modificiations to allow instead.
* Have to specify version manually in the version variable. Check here for latest version: https://www.minecraft.net/en-us/download/server/bedrock I hope to have this automatically grab the latest version in the future.

# How to use this playbook

Run the playbook with this command, substituting your credentials. -k is used to prompt for user password, -K is for the sudo/become password. If you want to use a private key switch out -k (lowercase) with --key-file <path>. This playbook requires become as it will install packages and create services.

```
ansible-playbook -i hosts deploy_minecraft.yml -u <username> -k -K
```

# If installing bedrock connect
* first you'll need to install the mysql role by @geerlingguy (or your own mysql setup), and include the requisite variable configurations in group_vars/all.yml. Also, bedrock connect needs to run on port 19132, you have to set bedrock_server to run on a different port.

```
ansible-galaxy role install -r roles/requirements.yml
```

Set the vars as required if any are different from defaults. Change these in `group_vars/all.yml', or setup specific group/host vars if you are looking for a scaled deployment.

# Configurable variables
* gamertags are case-sensitive and might contain spaces. Double check these careully if you want to use the whitelist.
* as a preventative measure, whitelist is not automatically enabled if whitelist is created. Set the `whitelist` variable to enable it when you are sure it's correct.
* pay attention to variables that use quote around true/false values. Make sure to preserve the "" or the templates may not work correctly. These are strings, not literal true/false values.

```
# current latest version. Get from https://www.minecraft.net/en-us/download/server/bedrock
minecraft_version: 1.16.100.04

# will be used to name not only the server but folders and services.
minecraft_server_name: my_server

# this setting will depend on resource levels of your host machine. More players = more resources.
max_players: 10

gamemode: survival

difficulty: easy

# users must log in to their microsoft account to connect
online_mode: "true"

# enables/enforces the whitelist on the server
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
