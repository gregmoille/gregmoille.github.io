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


In there you will find out how to setup a jupyterhub using google authentification to allow only selected users. The section covers here are: 

<!-- TOC -->

- [Setting up all the user accounts](#setting-up-all-the-user-accounts)
- [Installing Jupyter, JupyterLab, JupyterHub](#installing-jupyter-jupyterlab-jupyterhub)
- [Setting up Google API](#setting-up-google-api)
- [Serving JupyterHub through a website with nginx](#serving-jupyterhub-through-a-website-with-nginx)
- [Extra: Visual Code Studio in JupyterHub](#extra-visual-code-studio-in-jupyterhub)
- [Reference found on the web](#reference-found-on-the-web)

<!-- /TOC -->


# Setting up all the user accounts

Usually you would like to make sure that the security of your jupytherhub system is secure such that no other users than the one approved can login. In particular with jupyter-like server which can access terminal. Therefore it is important to emphasize that unwanted user could in theory take full control of your machine. 


To this extend, using google oauth2 authentification is a convenient and secure way to identify user.

Another trick found while seting up our own jupyterhub server is - in adition of the google oauth2 - only allow user that exist on the  machine. If the user doesn't exist yet the google identification go through, this would yield an error blocking the access to the server. It is not super pretty, but this work. 


Therefore, before starting setting up our server, let's create our users and their home directory. For linux users, You can ue the script below which automatically creates all the users:

`create_users.sh`
```bash
#! /bin/bash

for uu in nphoton gmoille perezed kartiks tsrahman khoang98 jstone12
do
  adduser -m ${uu}
  echo "${uu}:$1" | chpasswd
  mkdir /home/${uu}/AmunMount
  chown ${uu} /home/${uu}/AmunMount
done
```

 Make the script executable and run `sudo ./create_users.sh <passwrod>`, where you replace _\<password\>_ with the password you want for the users. This password won't be used further 


 For MacOS and Windows user, create the user manually, sorry 


# Installing Jupyter, JupyterLab, JupyterHub

Let's get to the core of this tutorial: installing the server! 



# Setting up Google API

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

# Reference found on the web 

