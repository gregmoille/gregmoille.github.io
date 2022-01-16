---
title: 'How To Setup a JupyterHub'
date: 2022-01-15
permalink: /posts/2022/01/jupyterhubhow
tags:
  - Computer
  - Setting Up
  - jupyter
  - jupyterhub
---


This tutorial only stand for Linux/UNIX users, as jupyterhun only suport the former[^1]

In there you will find out how to setup a jupyterhub using google authentification to allow only selected users. The section covers here are: 

<!-- TOC -->

- [Setting up all the user accounts](#setting-up-all-the-user-accounts)
- [Installing Jupyter, JupyterLab, JupyterHub](#installing-jupyter-jupyterlab-jupyterhub)
- [Setting up JupyterHub to use Google OAuth](#setting-up-jupyterhub-to-use-google-oauth)
- [Setting Up Google API](#setting-up-google-api)
- [Creating a daemon for JupyterHub](#creating-a-daemon-for-jupyterhub)
- [Serving JupyterHub through a website with nginx](#serving-jupyterhub-through-a-website-with-nginx)
- [Extra: Visual Code Studio in JupyterHub](#extra-visual-code-studio-in-jupyterhub)

<!-- /TOC -->

This tutorial is relatively long becasue I go into a lot of details. However, setting a jupyterhub is super easy and super fast. It took me about 10min to setup the last one I put together.


# Setting up all the user accounts

Usually you would like to make sure that the security of your jupytherhub system is secure such that no other users than the one approved can login. In particular with jupyter-like server which can access terminal. Therefore it is important to emphasize that unwanted user could in theory take full control of your machine. 


To this extend, using google oauth2 authentification is a convenient and secure way to identify user.

Another trick found while seting up our own jupyterhub server is - in adition of the google oauth2 - only allow user that exist on the  machine. If the user doesn't exist yet the google identification go through, this would yield an error blocking the access to the server. It is not super pretty, but this work. 


Therefore, before starting setting up our server, let's create our users and their home directory. For linux users, You can ue the script `create_users.sh` below which automatically creates all the users:


```bash
#! /bin/bash

for uu in <list of users>
do
  adduser -m ${uu}
  echo "${uu}:$1" | chpasswd
  mkdir /home/${uu}/AmunMount
  chown ${uu} /home/${uu}/AmunMount
done
```

 Make the script executable and run `sudo ./create_users.sh <passwrod>`, where you replace _\<password\>_ with the password you want for the users. This password won't be used further. In the script change the _<list of users>_ to a list of username only separated by a space (no comma, no parantehsis or brackets, etc...). **The user name need to meet the <username>@domain.com format of the google user you will whitelist to the server**


# Installing Jupyter, JupyterLab, JupyterHub

Let's get to the core of this tutorial: installing the server! Most of this part has been taken out from the jupyterhub documentation[^1]. Pretty straightforward, install jupyter, jupyterlab and jupyter hub using your favorite python pacakge/environement manager

```bash
pip install jupyter notebook jupyterlab jupyterhub 
```
or 
```bash
pip3 install jupyter notebook jupyterlab jupyterhub 
```

JupyterHub relies on http proxy to tie the jupyter spanwer to the hub. To install it, first install nodejs
```bash
sudo apt-get install nodejs npm
```
if on debian-based distro, and if on fedora (and if you are not, just switch to it!)
```bash
sudo dnf install nodejs npm
```
Then install the http-proxy
```bash 
npm install -g configurable-http-proxy
```
And that's all! You have just installed jupyterhub that allow you to serve jupyter or jupyterlab for different users! Pretty neat uh? 

You can always check if everything is alright:
```bash
jupyterhub -h
configurable-http-proxy -h
```
# Setting up JupyterHub to use Google OAuth

Ok so far we have done half of the job. Now we need to setup the jupyterhub server such that it allows some users and refuse some other. 

Usually I tend to put my custom apps and config in the `/opt` folder, so if you prefer to do otherwise, change the basepath accordingly. 

To start let's install the python package for authentication 

```bash
pip install oauthenticator
```

First, let's create the folder where the config, cookie and database will be stored and create the configuration file

```bash
sudo mkdir /opt/jupyterhub
sudo chmod 775 /opt/jupyterhub
touch /opt/jupyterhub_config.py
```

The configuration file for jupyterhub is a regular python file that can be completely customized for a lot of things[^2], and an full example could be generated with `jupyterhub --generate-config`, yet we will not cover this here. Instead I will guide through what my setup is and minimum configuration for google oauth. 



The basic start of the file is: 

```python
import os
from oauthenticator.google import GoogleOAuthenticator
c = get_config()
c.JupyterHub.log_level = 10
```

Let's setup some few cookies and location for jupyterhub. Here we will also set the url and port for the http proxy that the jub use to connect to the individual jupyterhub. This port will be needed when [we set the daemon](#creating-a-daemon-for-jupyterhub). We also set another security level which is to delete any invalid user, never too cautious (although not sure it does anything)
```
c.JupyterHub.cookie_secret_file = '/opt/jupyterhub/jupyterhub_cookie_secret'
c.ConfigurableHTTPProxy.auth_token = '/opt/jupyterhub/proxy_auth_token'
c.ConfigurableHTTPProxy.api_url = 'http://localhost:5432'
c.Authenticator.delete_invalid_users = True
```

We can then import the `GoogleOAuthenticator` and start to set it up. Here we precise  _\<domain_name\>_ being the domain after the *@*. For instance, my email being gmoille@umd.edu, here I would replace it with `umd.edu`
```python
c.JupyterHub.authenticator_class = GoogleOAuthenticator
c.GoogleOAuthenticator.hosted_domain = ['<domain_name>']
```

Then you need to add the whitelist of users. Here you will need to append, for instance the following one where I put myself as an administrator:
```python
c.Authenticator.whitelist.users = {'gmoille',  'kartiks'}
c.Authenticator.admin_users = {'gmoille'}
```


Now we can add the parameter for the google authentication. We will go in more detail in the [next section](#setting-up-google-api)
```python
c.GoogleOAuthenticator.oauth_callback_url = 'https://<your_server>/hub/oauth_callback'
c.GoogleOAuthenticator.client_id = 'xxxxxxxxxxxxx'
c.GoogleOAuthenticator.client_secret = 'xxxxxxxxxxxxx'
```

Let's say you actually want anybody within your _\<domain_name\>_ to access it, even if you didn't create their user, you can automatically create new local user based on the google oauth using (found from[^3]):
```python
c.Authenticator.add_user_cmd = ['adduser', '-q', '--gecos', '""', '--disabled-password', '--force-badname']
```


# Setting Up Google API

# Creating a daemon for JupyterHub

Instead of running jupyterhub manually, it is convenient to create a systemd daemon that can be launched at boot and stop/restart using `systemctl`

To do so, create the service file 
```bash
sudo touch /etc/system/systemd/jupyterhub_server.service
```




# Serving JupyterHub through a website with nginx

# Extra: Visual Code Studio in JupyterHub

<!-- 
install configurable--http-proxy jupyterhub oauthenticator


reverse nginx

aoauth with google need to configur the dashboard for redirect handshake

install vcode studio in the browser : curl -fsSL https://code-server.dev/install.sh | sh

pip install jupyter-server-proxy

pip install jupyter-vscode-proxy

npmp for the reverse httpp -->

<!-- ```
user = os.getenv('JUPYTERHUB_USER')
c.JupyterHub.base_url = '/imhotep/'
c.JupyterHub.default_url = f'user/{user}/vscode'
```# Reference found on the web  -->

[^1]: [jupyterhub documentation](https://jupyterhub.readthedocs.io/en/stable/quickstart.html)

[^2]: [jupyterhub config file reference](https://jupyterhub.readthedocs.io/en/stable/reference/index.html)

[^3]: [python for undergraduate engineers](https://pythonforundergradengineers.com/add-google-oauth-and-system-service-to-jupyterhub.html)