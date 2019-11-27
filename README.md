# quick-nginx Ansible Role

A quick and easy way to get nginx running on your server using Ansible. Comes with an opinionated set of defaults but is easily extensible to more configurations.

## Motivation

I just want an nginx reverse proxy with a sane set defaults. I don't want to configure a thousand different nginx settings. It's the 21st century, so naturally we should have all of these in our reverse proxy by default:
* SSL/TLS certs that auto-renew with 0 downtime via LetsEncrypt
* Automatic HTTP -> HTTPS upgrade
* HSTS enabled

## Example
```yml
- hosts: all
	roles:
		- quick-nginx
	vars:
		quick_nginx:
			sites:
				my_site:
					domains:
						- mysite.mydomain.com
					mode: proxy_pass
					upstream: 127.0.0.1:3000
					letsencrypt_email: certs@mydomain.com
```
