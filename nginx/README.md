# nginx

nginx web server, with one to many websites, for FreeBSD 10,11 and Debian 7,8

* `conf.d/` ready
* `modules.d/*.conf` for dynamic modules
* list of networks `proxified_by` will be trusted as reverse-proxy
  * include HTTPS support for fastcgi

# Features

* nginx on FreeBSD, Debian
* default server
* monitoring (on `/nginx_status` for `{monitoring_from}` IP's)

# example

## minimal example:
<pre>
- hosts: webhost1
  roles:
    - nginx
  vars:
    sites:
      # this will create an empty website hosted in /home/mysite/mysite:
      - { id: mysite, name: website.example.domain }
      # another one hosted in /usr/local/www/other:
      - { id: myothersite, name: othersite.example.domain, rootdir: /usr/local/www/other }
</pre>

# Role Variables

## global (default value)

* `nginx_processes` (1)
   as in nginx.conf
* `error_loglevel` (info)
* `www_default_root` (/var/www/default)
* `nginx_log_dir` (/var/log/nginx)
* `nginx_modules` ([])
  nginx dynamic modules to include
* `sites` ([])
  list of sites dicts (see below)
* `nginx_status_path` (`/nginx_status`)
* `monitoring_from` ([127.0.0.1])

### if behind reverse-proxy

* `proxified_by` ([])
  list of networks to be trusted as reverse-proxies:
  - HTTPS accelerator included via X-Forwarded-Proto header
  - original client IP kept via realip module

## per site

### mandatory (can't work without…)

* `id` (no default) 
  unique (per host) short name used everywhere by default
* `name` (no default)
  DNS name

### optional (default values can suffice)

* `rootdir` (/home/{{id}}/{{id}})
  web root
* `listen` (80)
  port or ip:port to listen to

### optional (no default)

* `nginx_includes`
  files or templates included inside `server {}` block
  see *files locations* for path

#### TLS / HTTP2 (all or none)

* `tls_hsts` (31536000 if `x509_cert`, else 0)
  if > 0, add Strict-Transport-Security header
* `tls_redirect` (False)
  if True, redirect all http requests to https
* `x509_cert`
  x509 certificate (with intermediate certs)
* `x509_key`
  private key for tls/http2
* `x509_cachain`
  complete ca chain for stapling
  (from root CA to last intermediate)

# Files / Templates locations

All files matching `playbooks/files/{{id}}/nginx/conf.d/*.conf` will be copied in nginx's conf.d directory and included in 'http {}' context

The first file found in this list will be used as site config (per site)

- `playbooks/templates/{{id}}/site.conf.j2`
- `playbooks/files/{{id}}/site.conf`
- `playbooks/templates/{{inventory_hostname without last digit}}/{{id}}.conf.j2`
- `playbooks/files/{{inventory_hostname without last digit}}/{{id}}.conf`
- `roles/nginx/templates/site.conf.j2`

For `includes` files, they will be searched in (first match wins):

- `playbooks/templates/{{id}}/nginx/`
- `playbooks/files/{{id}}/nginx/`
- `playbooks/files/{{inventory_hostname without last digit}}/nginx/`
- `playbooks/files/{{inventory_hostname without last digit}}/nginx/`

other relative paths (`x509_cert`, …) will be searched "normally"
(https://github.com/ansible/ansible/issues/14341#issuecomment-234559431)

