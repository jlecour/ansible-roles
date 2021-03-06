# Role php-fpm

For Freebsd and Debian

Installs php-fpm and one or many pools using 'site' configs.

Calls webserver role (nginx by default) per site to create HTTP confs

# Variables (default value)

* `monitoring_from` ([127.0.0.1])
  host(s) or net(s) allowed to query status url's
* `fpm_priority` (12)
  default processes priority
* `fpm_error_log` (syslog)
  error log filename or "syslog"
* `fpm_log_level` (notice)
  log level (alert, error, warning, notice, debug)
* `www_user` and `www_group` (system dependant)
  webserver user and group

## per-site variables (default value)

### mandatory

* `id` (mandatory, no default)
  Unique identifier on the host for this config, used for user, group, maintainer, dirsnames, …)
* `name` (mandatory, no default)
  DNS domain for this site

### optional (default value)

* `webserver_role` (nginx)
  Which webserver role to use (see this role's README.md for additional vars)
* `user` (_{{id}})
  System user to run php
* `maintainer` ({{id}})
  Maintenance user for the app (with shell and home)
* `group` (_{{id}})
  Common group for user and maintainer
* `home` (/home/{{id}})
  Home of 'maintainer' and base for other default values (tmpdir, rootdir, ...)
* `tmpdir` (/home/{{id}}/tmp)
  Base for tmp, sessions, uploads
* `limit_openbasedir` (True)
  activate php `open_basedir` limitation: limits fopen to tmpdir+rootdir+`pear_path`+`open_basedir_add`
* `open_basedir_add` ('')
  path(es) to add to php `open_basedir` (string, separated by :)
* `upload_max_meg` (20)
  base to compute php `post_max_size` and `upload_max_filesize`, in Mo
* `php_values` ([])
  additional php values for php.ini
* `php_flags` ([])
  additional php flags for php.ini
* `php_admin_values` ([])
  additional forced `php_values`
* `php_admin_flags` ([])
  additional forced `php_flags`
* `error_log` (`{{fpm_error_log}}`)
  error log file for this site

#### fpm specifics
see http://php.net/manual/en/install.fpm.configuration.php

* `debug` (False)
  activate `catch_workers_output` for this site
* `pm` (ondemand)
  process manager model
* `pm_max` (200)
  max processes
* `pm_max_requests` (500)
  max requests served before restarting process (limits memory leaks)
* `pm_min` (2)
  for 'dynamic' pm: min processes waiting
* `pm_start` (5)
  for 'dynamic' pm: num processes started at boot
* `pm_max_spares` (10)
  for 'dynamic' pm: max idle processes
* `fcgi_status_path` (/fpmstatus.php)
  path to reach fpm's status report
  (restricted to `monitoring_from` nets by webserver)
* `sessions_path` (/home/{{id}}/sessions)
  default path to store sessions
* `upload_dir` (/home/{{id}}/tmp)
  path to store temporary uploads
