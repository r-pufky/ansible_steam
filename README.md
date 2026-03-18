# Steam Manager
Setup and manage steamcmd, wine, and winetricks for hosting dedicated servers.

## [Requirements][i]
Requires [r_pufky.game][g] galaxy-ng collection. See
[reference documentation][h] for troubleshooting and config variables.

* Steamcmd: 60MB
* Metamod: ~5MB
* Sourcemod: ~200MB
* Wine: ~2GB

## Role Variables
Detailed variable use documented in defaults. See usage for role operation.

* [defaults][j] - User configurable options.

## Usage
**en_US.UTF-8** locale will be installed.

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                  | Notes
 ------|-----------------------|-------
  1    | steam_flg_apt_sources | Force required APT sources.
  2    | steam_flg_update      | Force steamcmd/wine updates if already installed.
  3    | steam_flg_metamod     | Install Metamod.
  4    | steam_flg_sourcemod   | Install Sourcemod.
  5    | steam_flg_wine        | Install wine/winetricks.

### Context Variables
After successful execution the following variables are available for further
use during setup of dedicated servers (standard role variable scope):

  Variable                      | type        | Description
 -------------------------------|-------------|-------------
  steam__ctx._user              | str         | Resolved user name.
  steam__ctx._group             | str         | Resolved group name.
  steam__ctx._uid               | str         | Resolved user UID.
  steam__ctx._gid               | str         | Resolved group GID.
  steam__ctx._home              | str         | Absolute path resolved home dir.
  steam__ctx._share             | str         | Absolute path resolved local shared dir.
  steam__ctx._metamod_package   | str         | Metamod archive filename.
  steam__ctx._metamod_archive   | str         | Absolute path to Metamod archive.
  steam__ctx._metamod_version   | str         | Metamod parsed version string.
  steam__ctx._metamod_dirs      | list of str | Relative Metamod install directories.
  steam__ctx._metamod_files     | list of str | Relative Metamod install files.
  steam__ctx._sourcemod_package | str         | Sourcemod archive filename.
  steam__ctx._sourcemod_archive | str         | Absolute path to Sourcemod archive.
  steam__ctx._sourcemod_version | str         | Sourcemod parsed version string.
  steam__ctx._sourcemod_dirs    | list of str | Relative Sourcemod install directories.
  steam__ctx._sourcemod_files   | list of str | Relative Sourcemod install files.

> NOTE:
> Each mod installs portions of the other when unpacking, resulting in files
> that may need to be removed between steps, depending on the specific game
> setup. These files are tracked to simplify dedicated server setup.

### Example Playbooks

## Example Playbook
This role is intended to be called to setup base requirements for running a
dedicated server on linux. `steamcmd` is ready to be executed to install
dedicated servers.

roles/my_custom_role/tasks/task.yml
``` yaml
- name: 'Install steamcmd'
  ansible.builtin.import_role:
    name: 'r_pufky.game.steam'

- name: 'Caching steam context only needed if executing role multiple times.'
  ansible.builtin.set_fact:
    cached_context: '{{ steam__ctx }}'

- name: 'Install dedicated linux server'
  ansible.builtin.shell: >-
    /usr/games/steamcmd
    +@sSteamCmdForcePlatformType linux
    +force_install_dir /home/steam/my_server
    +login anonymous
    +app_update 1234567
    +quit
  args:
    executable: '/bin/bash'
    chdir: '{{ steam__ctx._home }}'
  changed_when: false
  become: true
  become_user: '{{ steam__ctx._user }}'
```

Windows dedicated servers may be installed and run via Wine:
``` yaml
- name: 'Install steamcmd with wine, winetricks'
  ansible.builtin.import_role:
    name: 'r_pufky.game.steam'
  vars:
    steam_flg_wine: true

- name: 'Install dedicated windows server'
  when: seven_days_to_die_srv_update_server
  ansible.builtin.shell: >-
    /usr/games/steamcmd
    +@sSteamCmdForcePlatformType windows
    +force_install_dir /home/steam/my_server
    +login anonymous
    +app_update 1234567
    +quit
  args:
    executable: '/bin/bash'
    chdir: '{{ steam__ctx.home }}'
  changed_when: false
  become: true
  become_user: '{{ steam__ctx.user }}'
```

## Development
Configure [environment][a].

``` bash
# Run all tests.
molecule test --all
```

Testing variables:

  Variable          | type | Description
 -------------------|------|-------------
  url_inject_enable | bool | Disable **get_url** to inject files locally.

### [Releases][b]
Focused on service deployment with templated configuration to minimize role
churn due to inconsistent and rapid rolling release cycle.

  Release | Debian | Ansible | Notes
 ---------|--------|---------|-------
  1.x.x   | 13     | 2.20    | Ansible 2.20, feature flags, and semantic versioning.
  0.x.x   | 12     | 2.11    | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License][c] | [direct link][f]

## Author Information
PGP: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9][d] | [github gist][e]


[a]: https://r-pufky.github.io/ansible_docs
[b]: https://semver.org/spec/v2.0.0
[c]: https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0
[d]: https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9
[e]: https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22

[f]: https://github.com/r-pufky/ansible_steam/blob/main/LICENSE
[g]: https://github.com/r-pufky/ansible_collection_game
[h]: https://r-pufky.github.io/docs/games/steam
[i]: https://github.com/r-pufky/ansible_steam/blob/main/meta/main.yml
[j]: https://github.com/r-pufky/ansible_steam/tree/main/defaults/main/main.yml