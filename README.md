# minecraft_bedrock_deploy

default structure for ansible

Sets up a minecraft bedrock server on specified version on linux systems running systemd. Minecraft server will run as a service and automatically start on boot.

```
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