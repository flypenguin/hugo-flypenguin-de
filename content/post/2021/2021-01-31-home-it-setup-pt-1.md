---
title: Home IT setup, pt. 1
author: penguin
type: post
date: 2021-02-18T21:24:27+01:00
draft: true
url: /2020/02/18/home-it-setup-pt-1
categories:
  - Uncategorized
tags:
  - home
---
Task:

  * use one small Intel NUC for multiple services in your home (Home Assistant, Unifi controller, etc.)
  * Get valid TLS certificates for these services using let's encrypt

## 1 - System layout

I'm using a systemd service which is able to run existing docker-compose files in a certain location as services

CODE

## 2 - 

  * https://cert-manager.io/docs/configuration/acme/dns01/route53/