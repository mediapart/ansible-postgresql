## ANXS - PostgreSQL [![Build Status](https://github.com/ANXS/postgresql/actions/workflows/ci.yml/badge.svg)](https://github.com/ANXS/postgresql/actions/workflows/ci.yml)

---
Help Wanted! If you are able and willing to help maintain this Ansible role then please open a GitHub issue. A lot of people seem to use this role and we (quite obviously) need assistance!
💖
---

Ansible role which installs and configures PostgreSQL, extensions, databases and users.


#### Installation

This has been tested on Ansible 2.4.0 and higher.

To install:

```
ansible-galaxy install anxs.postgresql
```

#### Example Playbook

An example how to include this role:

```yml
---
- hosts: postgresql-server
  roles:
    - role: ANXS.postgresql
      become: yes
```

An example how to include this role as a task:

```yml
---
- hosts: postgresql-server
  tasks:
    - block: # workaround, see https://stackoverflow.com/a/56558842
        - name: PSQL installation and configuration
          include_role:
            name: ANXS.postgresql
          vars:
            postgresql_users:
              - name: abc
                password: abc
      become: true
```

#### Dependencies

- ANXS.monit ([Galaxy](https://galaxy.ansible.com/list#/roles/502)/[GH](https://github.com/ANXS/monit)) if you want monit protection (in that case, you should set `monit_protection: true`)


#### Compatibility matrix

| Distribution / PostgreSQL |     11     |         12         |         13         |         14         |         15         |         16         |         17         |
| ------------------------- | :--------: | :----------------: | :----------------: | :----------------: | :----------------: | :----------------: | :----------------: |
| Debian 11.x               | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |
| Debian 12.x               | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |
| Rockylinux 8.x            | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |
| Rockylinux 9.x            | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |
| Ubuntu 20.04.x            | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |
| Ubuntu 22.04.x            | :no_entry: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |    :interrobang:   |


- :white_check_mark: - tested, works fine
- :warning: - not for production use
- :grey_question: - will work in the future (help out if you can)
- :interrobang: - maybe works, not tested
- :no_entry: - has reached End of Life (EOL)


#### Variables

```yaml
# Basic settings
postgresql_version: 17
postgresql_encoding: "UTF-8"
postgresql_locale: "en_US.UTF-8"
postgresql_ctype: "en_US.UTF-8"

postgresql_admin_user: "postgres"
postgresql_default_auth_method: "peer"

postgresql_service_restarted_state: "reloaded" # state of the service after configuration changes (e.g. restarted, reloaded).

postgresql_cluster_name: main
postgresql_cluster_reset: false

postgresql_log_directory_group: "{{ postgresql_service_group }}" # group of the `postgresql_log_directory`.
postgresql_log_directory_mode: 0700 # permissions of the `postgresql_log_directory`.

# List of databases to be created (optional)
# Note: for more flexibility with extensions use the postgresql_database_extensions setting.
postgresql_databases:
  - name: foobar
    owner: baz          # optional; specify the owner of the database
    hstore: yes         # flag to install the hstore extension on this database (yes/no)
    uuid_ossp: yes      # flag to install the uuid-ossp extension on this database (yes/no)
    citext: yes         # flag to install the citext extension on this database (yes/no)
    encoding: "UTF-8"   # override global {{ postgresql_encoding }} variable per database
    lc_collate: "en_GB.UTF-8"   # override global {{ postgresql_locale }} variable per database
    lc_ctype: "en_GB.UTF-8"     # override global {{ postgresql_ctype }} variable per database

# List of database extensions to be created (optional)
postgresql_database_extensions:
  - db: foobar
    extensions:
      - hstore
      - citext

# List of users to be created (optional)
postgresql_users:
  - name: baz
    pass: pass
    encrypted: yes  # if password should be encrypted, postgresql >= 10 does only accepts encrypted passwords
    role_attr_flags: "login" # comma separated list of role attributes ([no]superuser,[no]createrole,[no]createdb,[no]inherit,[no]login,[no]replication,[no]bypassrls,)

# List of schemas to be created (optional)
postgresql_database_schemas:
  - database: foobar           # database name
    schema: acme               # schema name

  - database: foobar           # database name
    schema: acme_baz           # schema name
    owner: baz                 # owner name

# List of user privileges to be applied (optional)
postgresql_user_privileges:
  - roles: baz                  # comma separated list of role (user/group) names to set permissions for.
    database: foobar            # name of database to connect to.
    schema: acme_baz            # schema that contains the database objects specified via objs (see documentation for details).
    type: table                 # type of database object to set privileges on. e.g.: table (default), sequence, function,... (see documentation for details)
    objs: employee              # comma separated list of database objects to set privileges on (see documentation for details).
    privs: "ALL"                # comma separated list of privileges to grant. e.g.: INSERT,UPDATE/ALL/USAGE
```

There's a lot more knobs and bolts to set, which you can find in the [defaults/main.yml](./defaults/main.yml)


#### Testing - Molecule

This project comes with a molecule configuration. Please see [./molecule/README.md](./molecule/README.md)

Examples:

```
molecule test
```

#### Testing - Vagrant

This project comes with a Vagrantfile, this is a fast and easy way to test changes to the role, fire it up with `vagrant up`

See [vagrant docs](https://docs.vagrantup.com/v2/) for getting setup with vagrant

Once your VM is up, you can reprovision it using either `vagrant provision`, or `ansible-playbook tests/playbook.yml -i vagrant-inventory`

If you want to toy with the test play, see [tests/playbook.yml](./tests/playbook.yml), and change the variables in [tests/vars.yml](./tests/vars.yml)

If you are contributing, please first test your changes within the vagrant environment, (using the targeted distribution), and if possible, ensure your change is covered in the tests found in [.travis.yml](./.travis.yml)

#### License

Licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

#### Thanks

Creator:
- [Pjan Vandaele](https://github.com/pjan)

Maintainers:
- [Jonathan Lozada D.](https://github.com/jlozadad)
- [Jonathan Freedman](https://github.com/otakup0pe)
- [Sergei Antipov](https://github.com/UnderGreen)
- [Greg Clough](https://github.com/gclough)
- [Magnus Lübeck](https://github.com/maglub)
- [Leo C.](https://github.com/MrMegaNova)
- [Laurent Lavaud](https://github.com/fidelio33b)

Top Contributors:
- [David Farrington](https://github.com/farridav)
- [Jesse Lang](https://github.com/jesselang)
- [Michael Conrad](https://github.com/MichaelConrad)
- [Sébastien Alix](https://github.com/sebalix)
- [Copperfield](https://github.com/Copperfield)
- [T. Soulabail](https://github.com/tsoulabail)
- [Ralph von der Heyden](https://github.com/ralph)


#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/ANXS/postgresql/issues)!
