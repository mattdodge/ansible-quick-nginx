# quick_nginx Ansible Role

A quick and easy way to get nginx running on your server using Ansible. Comes with an opinionated set of defaults but is easily extensible to more configurations.

## Motivation

I just want an nginx reverse proxy with a sane set defaults. I don't want to configure a thousand different nginx settings. It's the 21st century, so naturally we should have all of these in our reverse proxy by default:
* SSL/TLS certs that auto-renew with 0 downtime via LetsEncrypt
* Automatic HTTP -> HTTPS redirect
* HSTS enabled

## Installation
Via ansible-galaxy
```bash
ansible-galaxy install mattdodge.quick_nginx
```

## Quick Example
Here's the config for a reverse proxy that will proxy pass requests to https://mysite.mydomain.com to the service running at http://127.0.0.1:3000.
```yml
- hosts: all
  roles:
    - quick-nginx
  vars:
    quick_nginx:
      my_site:
        domains:
          - mysite.mydomain.com
        upstream: 127.0.0.1:3000
        letsencrypt_email: certs@mydomain.com
```

## Full Config
```yml
quick_nginx:
  my_site:          # A unique key to use for this site. Multiple nginx sites are allowed to coexist in the quick_nginx dict
    domains:        # A list of domains to access this site at
      - mysite.mydomain.com
      - www.mysite.mydomain.com
    mode: proxy_pass                # The mode to operate this site in, see Modes documentation for more examples
    upstream: 127.0.0.1:3000        # An upstream to proxy requests too. Only makes sense in the proxy_pass mode
    letsencrypt_email: certs@mydomain.com    # An email to use when registering LetsEncrypt certs
quick_nginx_letsencrypt: True       # Set to False if you don't want LetsEncrypt certs, you're on your own
```

## Modes
This role can operate nginx in multiple modes. The default mode is `proxy_pass`, which will proxy requests to a backend/upstream somewhere.

You can specify the mode of a site by passing the `mode` variable to the site's config, like so:
```yml
quick_nginx:
  my_site:
    domains:
      - mysite.mydomain.com
    mode: proxy_pass
```

### Available Modes

The following modes are available in the repo by default. Each contains some variables/configuration specific to the mode.

* **proxy_pass** - Proxy requests to an upstream/backend
  * **upstream** - The hostname and port of the upstream to proxy requests to. Proxy pass will proxy via HTTP even though incoming requests to the nginx server may occur over HTTPS.
* **redirect** - Perform a 301 HTTP redirect to a different site
  * **redirect_domain** - The domain name to redirect all incoming requests to this site to. Paths and query strings will be preserved.

### Creating New Modes
A mode is pretty much just a mapping to an nginx configuration template; these live in `templates/nginx`. To create a new mode, for example `no_ssl`, create a new template at `templates/nginx/no_ssl.conf.j2`. Now you can pass `mode: no_ssl` to a site to use that nginx template instead. Of course, Ansible variables are available for use in your template.
