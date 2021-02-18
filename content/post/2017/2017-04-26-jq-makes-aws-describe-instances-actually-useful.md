---
title: jq makes AWS “describe-instances” actually useful
author: penguin
type: post
date: 2017-04-26T12:33:04+00:00
url: /2017/04/26/jq-makes-aws-describe-instances-actually-useful/
categories:
  - Snippets
tags:
  - aws
  - jq

---
Just so I don't forget 🙂

<pre class="lang:default decode:true " title="jq with aws ec2 describe-instances">aws ec2 describe-instances | \
  jq '.Reservations[].Instances[] | {IP: .PrivateIpAddress, ID: .InstanceId, Name: .Tags[] | select(.Key=="Name").Value}'</pre>

&nbsp;