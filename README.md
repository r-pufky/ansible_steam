# Steam Manager
Setup and manage steamcmd, wine, and winetricks for hosting dedicated servers.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_steam/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_steam/tree/main/defaults/main/)

### Generated Variables
After successful execution the following variables are available for further
use during setup of dedicated servers (standard role variable scope):

 Variable              | type | Description
-----------------------|------|-----------------------------------------
 _steam_srv_user       | dict | Resolved steam user metadata.
   .raw                | str  | Resolved steam user name.
   .role_uid           | str  | Resolved steam user UID.
   .role_home          | str  | Resolved steam user home directory.
   .role_share         | str  | Resolved steam user local shared directory.
 _steam_srv_group      | dict | Resolved steam group metadata.
   .raw                | str  | Resolved steam group name.
   .role_gid           | str  | Resolved steam group GID.

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.deb](https://github.com/r-pufky/ansible_collection_deb) collection.

## Example Playbook
This role is intended to be called to setup base requirements for running a
dedicated server on linux. `steamcmd` is ready to be executable to install
dedicated servers.

roles/my_custom_role/tasks/task.yml
``` yaml
- name: 'Install | steamcmd'
  ansible.builtin.import_role:
    name: 'r_pufky.game.steam'

- name: 'Install | cache steam user'
  ansible.builtin.set_fact:
    my_server:
      user: '{{ _steam_srv_user.raw }}'
      uid: '{{ _steam_srv_user.role_uid }}'
      group: '{{ _steam_srv_group.raw }}'
      gid: '{{ _steam_srv_group.role_gid }}'
      home: '{{ _steam_srv_user.role_home }}'
      share: '{{ _steam_srv_user.role_share }}'
      saves: '{{ _steam_srv_user.role_share ~ "/7DaysToDie/Saves" }}'

- name: 'Install | install dedicated server'
  ansible.builtin.shell: >-
    /usr/games/steamcmd
    +@sSteamCmdForcePlatformType linux
    +force_install_dir /home/steam/my_server
    +login anonymous
    +app_update 1234567
    +quit
  args:
    executable: '/bin/bash'
    chdir: '{{ my_server.home }}'
  changed_when: false
  become: true
  become_user: '{{ my_server.user }}'
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_docs/blob/main/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

### Releases
Major release versions track Debian release versions:

* **[13.x.x](https://github.com/r-pufky/ansible_steam)**: 13 Trixie.
* **[12.x.x](https://github.com/r-pufky/ansible_steam/tree/12.x)**: 12 Bookworm.

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_steam/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
