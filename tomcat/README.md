# tomcat

FreeBSD & Debian, tomcat 8, jdk8, memcache sessions, remoteipvalve support

## general config

* RemoteIpValve from:
  * `reverse_proxies` (array of IP's)
* `classpath_adds`: jar's list to be added
* `tomcat_java_opts`: java command opts
* listen on 8080 (`tomcat_http_port`) and 8009 (`tomcat_ajp_port`)
* see `defaults/main.yml` for exhaustive list

## http(s) reverse-proxy support

if `proxies_ips_regex` is not empty:

* RemoteIpValve will be configured, supporting:
  * X-Forwarded-For
  * X-Forwarded-Proto (must be 'https' if ssl)

## memcache sessions

if `tomcat_memcached_nodes` is not empty:

Session are replicated between memcache instances on each tomcat host 
Using https://github.com/magro/memcached-session-manager/wiki

* `tomcat_memcached_nodes` has to be filled as this:
<code><pre>tomcat_memcached_nodes: 'japps3:japps3.serv.int:11211,japps4:japps4.serv.int:11211'</pre></code>
* node names (here japps3/japps4) *MUST* match inventory `ansible_hostname`

(see templates/context.xml.j2)

## webapps deployment

* `tomcat_webapps` is a dict of webapps to deploy (defaults to empty)
  * `key` is name
  * `war` is war file to deploy in webapps dir
  * `gitsrc` will be cloned in dir `gitdst` if both exists
  * `script` is the command line, relative to `gitdst`, to be launched (as root) user after deploying (if any)

