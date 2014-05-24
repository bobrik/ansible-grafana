grafana
========

[grafana](https://github.com/torkelo/grafana) role for ansible.

Features
------------

* Protected with nginx and http basic auth
* Read-only access for data and read-write for dashboards
* Many instances per server could be deployed
* SSL ready, https is forced if enabled

Role Variables
--------------

* `grafana_owner` basically nginx user, `www-data` by default
* `grafana_version` if defined, install this version via binary rather than building from git
* `grafana_git_url` git url for grafana, set to upstream by default
* `grafana_git_branch` git branch to track, set to master by default
* `grafana_root_path` path to clone grafana, `/var/www/grafana` by default
* `grafana_default_route` default dashboard url, `/dashboard/file/default.json` by default
* `grafana_index` grafana index to store dashboards, `grafana-dash` by default
* `grafana_elasticsearch_url` elasticsearch url *for nginx*, http://127.0.0.1:9200 by default
* `grafana_graphite_url` graphite-web url *for nginx*, http://127.0.0.1:8080 by default
* `grafana_nginx_config_name` nginx config name, `grafana.conf` by default
* `grafana_nginx_config_path` nginx configs dir, `/etc/nginx/sites-enabled` by default
* `grafana_nginx_listen` nginx listen address, `127.0.0.1` by default
* `grafana_nginx_server_name` nginx server_name (hostname), `127.0.0.1` by default
* `grafana_nginx_access_log` path to nginx access_log, `false` by default
* `grafana_nginx_error_log` path to nginx error_log, `false` by default
* `grafana_nginx_enable_ssl` whether or not ssl should be enabled, `false` by default
* `grafana_nginx_ssl_cert_path` nginx ssl certificate path, `""` by default
* `grafana_nginx_ssl_key_path` nginx ssl key path, `""` by default
* `grafana_nginx_http_auth_file` path to nginx http auth file, `false` by default

Minimal installation on ubuntu requires none of variables to be set, it will work on `http://127.0.0.1/`.

Dependencies
------------

* nginx
* elasticsearch
* (optional) node.js, required if `grafana_version` is not set.

Notes
-----

## Basic Authentication

Basic authentication may be set up with

```
apt: pkg=apache2-utils
command: httpasswd -bc {{ grafana_nginx_http_auth_file }} username password
```

## Self-Signed Certificate

You can create a self-signed certificate to use for SSL:

```
- command: openssl genrsa -out {{ grafana_nginx_ssl_key_path }} 2048 creates={{ grafana_nginx_ssl_key_path }}
- command: openssl req -new -key {{ grafana_nginx_ssl_key_path }} -out {{ grafana_nginx_ssl_csr_path }} -subj "/C={{ country_code }}/ST={{ state }}/L={{ location }}/O={{ organication }}/OU={{ organizational_unit }}/CN={{ cname }}" creates={{ grafana_nginx_ssl_csr_path }}
- command: openssl x509 -req -days 365 -in {{ grafana_nginx_ssl_csr_path }} -signkey {{ grafana_nginx_ssl_key_path }} -out {{ grafana_nginx_ssl_cert_path }} creates={{ grafana_nginx_ssl_cert_path }}
```

## Listen Address

The default installation only listens on 127.0.0.1, so is not externally accessible.  To make your server publicly accessible, set `grafana_nginx_listen` to `0.0.0.0`.  If you do this, it's strongly recommended that you also set `grafana_nginx_enable_ssl` and `grafana_nginx_http_auth_file` to password protect the site and ensure that the password is not sent in the clear.

License
-------

MIT

Author Information
------------------

* Ian Babrou, ibobrik@gmail.com
* Bryan Larsen, bryan@larsen.st
