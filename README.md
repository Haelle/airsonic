# Ansible Airsonic

Install [Docker Airsonic Advanced](https://hub.docker.com/r/airsonicadvanced/airsonic-advanced).

Warning: restarting Airsonic on a Raspberry Pi can take 3 min

## Nginx reverse proxy
Here is an example of configuration for Nginx :

**For HTTP**
```config
server {
  listen 80;
  listen [::]:80;

  server_name airsonic.domain.com;

  location / {
    proxy_pass              http://127.0.0.1:8080;
    proxy_set_header        Host $http_host;
    proxy_set_header        X-Real-IP  $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

**For HTTPS**

```config
server {
  listen 80;
  listen [::]:80;

  server_name airsonic.domain.com;

  if ($host = music.alxs.fr) {
    return 301 https://$host$request_uri;
  }
}

server {
  listen 443 ssl;

  server_name airsonic.domain.com;

  if ($scheme != "https") {
    return 301 https://$host$request_uri;
  }

  ssl_certificate /etc/letsencrypt/live/airsonic.domain.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/airsonic.domain.com/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

  location / {
    proxy_pass              http://127.0.0.1:8080;
    proxy_set_header        Host $http_host;
    proxy_set_header        X-Real-IP  $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

## Requirements

We need to install some Ansible collection via :

```sh
ansible-galaxy collection install -r requirements.yml
```

## Role Variables

## Example Playbook

```yaml
- hosts: localhost
  become: 'yes'
  roles:
    role: airsonic
```

## Linters
```shell
yamllint **/*.yml
ansible-lint **/*.yml
```

## Development usage
To run it locally create a symlink : `cd /etc/ansible/roles & sudo ln -s /path/to/airsonic .`

License
-------

BSD
