# MySQL

[![Build Status](https://drone.osshelp.ru/api/badges/ansible/mysql/status.svg)](https://drone.osshelp.ru/ansible/mysql)

Installs MySQL Distr/MySQL Community/Percona/MariaDB plus management of configuration/databases/etc.

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
- community_5.7
- community_8.0
- community_latest (installs latest available version)

### Configure only mode

``` yaml
    - role: mysql
      mysql_setup: configure
```

### Set root password and create .my.cnf file

``` yaml
    - role: mysql
      root_password: "{{ password_from_vault }}"
```

### Set MySQL common params

``` yaml
    - role: mysql
      params:
        - {key: innodb_file_per_table, value: 'on'}
```

### Create conf-enabled MySQL params files

``` yaml
    - role: mysql
      params:
        - {key: bind_address, value: 0.0.0.0}
      conf_available_params:
        prod:
          - {key: innodb_buffer_pool_size, value: '10G'}
          - {key: innodb_buffer_pool_instances, value: '10'}
        dev:
          - {key: innodb_buffer_pool_size, value: '256M'}
          - {key: innodb_buffer_pool_instances, value: '1'}
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

## TODO

- add parameters documentation
- split role
- tests
- percona 8, percona cluster
- focal support (percona-xtrabackup -> percona-xtrabackup-24, doesn't exist in distr repo)
- increase sanity of root password changing mechanism (see initial-setup script)
- installing root password mechanism is not working properly with percona_5.5 as version, needs to be tested and fixed
- remove workaround for percona 8.0 installation (pre_install_command). See [documentation](https://www.percona.com/doc/percona-server/LATEST/installation/apt_repo.html)
- percona-8.0. Idempotence test fail for "mysql : create users"
- check and fix logic in initial-setup.j2. useless `test -r "${my_cnf}" && {`
- check and fix systemd-override.j2 "ExecStartPre=" workaround. Add script from default ExecStartPre to initial-setup.j2
- check and fix mysql_set_root_password_command for all releases
