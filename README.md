# MySQL

[![Build Status](https://drone.osshelp.ru/api/badges/ansible/mysql/status.svg)](https://drone.osshelp.ru/ansible/mysql)

Installs MySQL Distr/MySQL Community/Percona/MariaDB plus management of configuration/databases/etc.

## Available parameters

### Main

| Param | Default | Description |
| -------- | -------- | -------- |
| `mysql_setup` | `full` | Setup mode. See [OSSHelp KB article](https://oss.help/kb4895) |
| `root_password` | `-` | MySQL root password. When defined .my.cnf will be genetated |
| `params` | `[]` | MySQL common params |
| `conf_available_params` | `[]` | MySQL conf-avalaible params |
| `databases` | `[]` | List of databases you want to create |
| `users` | `[]` | List of users you want to create |
| `mysql_no_restart` | `true` | Param for enabling MySQL restart (disabled by default) |
| `initial_setup` | `false` | When true initial-setup will be created (fixes for succesfully starting MySQL) |

## Deploy examples

### Install MySQL from dist repos

``` yaml
    - role: mysql
```

### Example

``` yaml
    - role: mysql
      version: required-version-here
```

Valid values for `required-version-here`:

- default (version from current distribution)
- percona_5.5
- percona_5.6
- percona_5.7
- percona_8.0
- maria_10.1
- maria_10.2
- maria_10.3
- maria_10.4
- maria_10.5 (bionic and focal only)
- community_5.7 (xenial and bionic only)
- community_8.0
- community_latest (installs latest available version)

### Set MySQL common params and create conf-enabled MySQL params files

``` yaml
    - role: mysql
      params:
        - { key: bind_address, value: 0.0.0.0 }
        - { key: innodb_file_per_table, value: 'on' }
      conf_available_params:
        prod:
          - { key: innodb_buffer_pool_size, value: '10G' }
          - { key: innodb_buffer_pool_instances, value: '10' }
        dev:
          - { key: innodb_buffer_pool_size, value: '256M' }
          - { key: innodb_buffer_pool_instances, value: '1' }
```

For more information see [knowledge base article](https://rm.osshelp.ru/projects/support-servers/knowledgebase/articles/2254#MySQL).

### Create databases and users

``` yaml
    - role: mysql
      databases:
        - name: ansible
      users:
        - name: ansible
          password: "{{ password_from_vault }}"

```

## FAQ

### Wrong root password in .my.cnf

If you see `youshouldchangethepassword123!` password in `/root/.my.cnf` , then your value for `initial_setup` is `true`, or you are using a MySQL base image. To set a password, define it in the lxhelper.yml in the profile section. See example below:

``` yaml
profiles:
  default:
    mysql:
      root-password: {{mysql_root_password}}
      root-host: '%'
```

### MySQL parameters are not being applied

The parameters for MySQL are applied after service restart, to enable service restart use the parameter `mysql_no_restart`.

## TODO

- split role
- tests
- percona 8, percona cluster
- increase sanity of root password changing mechanism (see initial-setup script)
- installing root password mechanism is not working properly with percona_5.5 as version, needs to be tested and fixed
- remove workaround for percona 8.0 installation (pre_install_command). See [documentation](https://www.percona.com/doc/percona-server/LATEST/installation/apt_repo.html)
- Idempotence test fails for "mysql : create users" on MySQL v8.x.x (community and percona)
- check and fix logic in initial-setup.j2. useless `test -r "${my_cnf}" && {`
- check and fix systemd-override.j2 "ExecStartPre=" workaround. Add script from default ExecStartPre to initial-setup.j2
- check and fix mysql_set_root_password_command for all releases
- deal with periodical 404 errors on percona-server packages downloads from repo.percona.com
