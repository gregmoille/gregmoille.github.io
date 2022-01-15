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


This tutorial only stand for Linux/UNIX users, as jupyterhun only suport the former [^1]
In there you will find out how to setup a jupyterhub using google authentification to allow only selected users. The section covers here are: 

<!-- TOC -->

- [Setting up all the user accounts](#setting-up-all-the-user-accounts)
- [Installing Jupyter, JupyterLab, JupyterHub](#installing-jupyter-jupyterlab-jupyterhub)
- [Setting up JupyterHub to use Google OAuth](#setting-up-jupyterhub-to-use-google-oauth)
- [Creating a daemon for JupyterHub](#creating-a-daemon-for-jupyterhub)
- [Serving JupyterHub through a website with nginx](#serving-jupyterhub-through-a-website-with-nginx)
- [Extra: Visual Code Studio in JupyterHub](#extra-visual-code-studio-in-jupyterhub)
- [Reference found on the web](#reference-found-on-the-web)

<!-- /TOC -->


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

Jupyterhub rely on http proxy to tie the jupyter spanwer to the hub. To install it, first install nodejs
```bash
sudo apt-get install nodejs npm
```
if on debian-based distro, and if on feodra (and if you are not, just switch to it!)
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

Ok so far we have done half of the job. Now we need to setup the jupyterhub server such that it allows some users and refus some other. 


# Creating a daemon for JupyterHub

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

[^1] [jupyterhub documentation](https://jupyterhub.readthedocs.io/en/stable/quickstart.html)