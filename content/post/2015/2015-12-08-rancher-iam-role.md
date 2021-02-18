---
title: Rancher IAM role
author: penguin
type: post
date: 2015-12-08T10:51:53+00:00
url: /2015/12/08/rancher-iam-role/
categories:
  - Snippets
tags:
  - aws
  - rancher

---
Rancher can create instances on EC2. If you want to define a dedicated IAM user for this, refer to [the Amazon docs][1] for the a profile template.

Unfortunately the first thing you get when using those permissions in rancher is "You are not authorized". Great. I'll update this when I know the correct permissions.

(Source: [Rancher docs][2])

 [1]: http://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html
 [2]: http://docs.rancher.com/os/amazon-ecs/