---
layout: page
title: "Nginx"
category: ops
date: 2014-05-29 19:05:05
---

For Uploading facility Nginx needs to be built with the nginx_upload_module. Unfortunately, nginx_upload_module is not available as a apttitude based plugin and Nginx needs to be compiled manually and compiled with the patch. Since compile & install can leave an unsolvable trail, its advised to do it via the package manager. apt-get allows building package from source and generating a dpkg file which can be managed by apt-get.

Add Repository

    sudo apt-get install dpkg-dev
    sudo add-apt-repository ppa:nginx/stable

Edit /etc/apt/sources.list.d/nginx-stable-<version>.list, add dpkg-src

    deb http://ppa.launchpad.net/nginx/stable/ubuntu <version> main
    deb-src http://ppa.launchpad.net/nginx/stable/ubuntu <version> main

Install Nginx-Source & Dependencies

    sudo apt-get update
    apt-get source nginx
    sudo apt-get build-dep nginx

Download Nginx Upload Module

    wget -P nginx-x.x/debian/modules http://www.grid.net.ru/nginx/download/nginx_upload_module-2.2.0.tar.gz

Add Target build rules

    Edit nginx-x.x/debian/rules and Add the Modules

    config.status.full: config.env.full config.sub config.guess
    ...
    --add-module=path/to/nginx_upload_module


Build the package

    cd nginx-x.x && dpkg-buildpackage -b

Install packages

    dpkg --install nginx-common_x.x.deb
    dpkg --install nginx-full_x.x.deb
