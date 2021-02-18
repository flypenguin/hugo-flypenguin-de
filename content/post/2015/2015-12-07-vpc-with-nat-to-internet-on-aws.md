---
title: VPC with NAT to internet on AWS
author: penguin
type: post
date: 2015-12-07T13:37:11+00:00
url: /2015/12/07/vpc-with-nat-to-internet-on-aws/
categories:
  - Snippets
tags:
  - aws
  - cloudformation

---
... and other TLAs.

Anyways, as far as I remember OpenStack does not need this, so I thought I document it here. I at least was surprised.

Situation: You want a private network sement in the cloud (in my case an Amazon VPC), and you don't want all hosts to be accessible from the internet. So you don't assign public IPs, and you need a router/gateway.

Amazon creates a network internet gateway, but this thing does _not_ do one thing: NATting. If your host does not have a private IP, it can't connect to "the internet".

Solution: You actually need to instantiate an EC2 instance, which you have to configure to do NAT (which is forwarding and masquerading) with a public IP address. And you have to set routing tables which point to that instance for all networks which should be inaccessible from the internet.

Thankfully there's [an article providing an example CloudFormation template][1].

Really, thanks.

Configuring the NAT instance is super-easy then. Amazon [mentions in its docs][2] that there are special Amazon Linux instances ("These AMIs include the string <code class="code">amzn-ami-vpc-nat</code> in their names [...]") which come with NATting preconfigured. Just instantiate an instance using the appropriate AMI image, and you're done. No further configuration needed.

 [1]: http://mrbluecoat.blogspot.de/2014/11/aws-cloudformation-template-for-vpc.html
 [2]: http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_NAT_Instance.html#basics