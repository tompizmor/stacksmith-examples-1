# Generic application stack template

In this example, you can find how to install a new service on top of the generic application stack template for Stacksmith. [Caddy](https://caddyserver.com/) is a general-purpose HTTP/2 web server that serves HTTPS by default. Between its features you can find:

- Easy configuration with the Caddyfile
- Automatic HTTPS on by default (via Let's Encrypt)
- HTTP/2 by default
- Virtual hosting so multiple sites just work

## Package and deploy with Stacksmith

1. Go to [stacksmith.bitnami.com](https://stacksmith.bitnami.com)
2. Create a new application and select the `Generic application (no pre-installed runtime)` Stack Template
3. Select the targets you are interested on (AWS, Kubernetes,...)
4. Upload in the `Application file` section the [_Caddyfile_](files/Caddyfile), [_caddy-control.sh_](files/caddy-control.sh) and [_index.html_](files/index.html) files from the [_files/_](files/) folder.
5. Upload the [_build.sh_](scripts/build.sh) and [_run.sh_](scripts/run.sh) scripts from the [_scripts/_](scripts/) folder.
6. Click the <kbd>Create</kbd> button

## Scripts

In the scripts folder, you can find the required scripts to run this service. 

### build.sh 

That file takes care of performing all the steps to install this new service in the machine.

- Installs Caddy using the script at https://getcaddy.com
- Creates the system user to run this server
- Creates the configuration file and the log folders
- Copies the script to control the service
- Copies the index.html page to the /var/www/ folder

### run.sh 

This file is ran to start the Web server service. It uses the `caddy-control.sh` script.

## Files

### Caddyfile

Main configuration file of Caddy. You can find more information about it in the [default documentation](https://caddyserver.com/docs/caddyfile). In this example, Caddy is configured this way:

- It accepts all the incoming requests to the 80 port
- The main site files are at `/var/www/`
- The log files are at `/var/log/caddy/`

### caddy-control.sh

Script file to manage the server. It makes the service to run using the `caddy` user.

### index.html

Example HTML site to show when accessing the server based on the [W3Schools Coming Soon Template](https://www.w3schools.com/w3css/tryw3css_templates_coming_soon.htm).

## Functionalities

### How to check the status of the Caddy service

This example solution includes a script file to control the server. You can easily get the status of the service by running this command:

```
/etc/caddy/caddy-control.sh status
```


### Configure the SSL certificates of your instance

In this example, the Caddy server is configured to accept all the inconming requests to the 80 port but it won't work when using the 443 port as it doesn't have a valid domain configured. Once you launch this example, you will need to follow these steps in order to make the application fully secure using HTTPS. 

- Point your DNS domain to the recently launched template's IP.
- [SSH your instance](https://stacksmith.bitnami.com/support/faq-aws)
- Modify the Caddy's main configuration file. You will find it at `/etc/caddy/Caddyfile`

```
https://mydomain.com

root /var/www/

log /var/log/caddy/caddy_log

errors /var/log/caddy/caddy_error

ext .html .htm .php .txt
```

As you can see, the first line refers to the URL that Caddy will use to serve the files at `/var/www/`.

> By default, the Caddy service uses `user@example.com` to register the new domains. In case you want to use your own email address to register your domains, please edit the control script at `/etc/caddy/caddy-control.sh`.

- Restart the Caddy service

```
/etc/caddy/caddy-control.sh restart
```
