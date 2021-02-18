---
title: Elastic Beanstalk with Docker using Terraform
author: penguin
type: post
date: 2017-04-13T16:14:18+00:00
url: /2017/04/13/elastic-beanstalk-with-docker-using-terraform/
categories:
  - Docker
  - Infrastructure
  - Snippets
tags:
  - aws
  - elastic beanstalk

---
I just investigate AWS Elastic Beanstalk. And I want to use terraform for this. This is what I've done, and how I've got it running. I basically do this because the docs for this are either super-long (and are still missing critical points) or super-short (and are also missing critical points), at least what I've found.

This should get you up and running in very little time. You can also get all the code from a [demo github repository][1].

#### General principles

The [Architectural Overview][2] is a good page to read to get an idea of what you're about to do. It's not that long.

In short, Elastic Beanstalk _runs a **version** of an **application** in an **environment**_. So the process is: Declaring an application, defining a couple of versions and environments, and then combine one specific version with one specific environment of an app to create an actually running deployment.

The environment is just a set of hosts configured in a special way (autoscaling & triggers, subnets, roles, etc.), whereas the application version is the info about how to deploy the containers on that environment (ports, env variables, etc.). Naturally, you think of having a DEV environment which runs "latest", and a PROD environment which runs "stable" or so. Go crazy.

#### Prerequisites & Preparation

For the example here you **need** a couple of things & facts:

  * An AWS account
  * In that account, you need: 
      * an S3 bucket to save your app versions
      * a VPC ID
      * subnet IDs for the instance networks
      * an IAM roles for the hosts
      * an IAM service roles elastic beanstalk. (see bottom for how to create that)
  * Terraform 🙂
  * The aws command line client

#### Get started

The files in the repository have _way_ more parameters, but this is the basic set which _should_ get you running (I tried once, then added all that stuff). The <span class="lang:default decode:true crayon-inline">main.tf</span>  file below will create the application and an environment associated with it.

<pre class="lang:default decode:true" title="main.tf"># file: main.tf

resource "aws_elastic_beanstalk_application" "test" {
  name        = "ebs-test"
  description = "Test of beanstalk deployment"
}

resource "aws_elastic_beanstalk_environment" "test_env" {
  name                = "ebs-test-env"
  application         = "ebs-test"
  cname_prefix        = "mytest"

  # the next line IS NOT RANDOM, see "final notes" at the bottom
  solution_stack_name = "64bit Amazon Linux 2016.09 v2.5.2 running Docker 1.12.6"

  # There are a LOT of settings, see here for the basic list:
  # https://is.gd/vfB51g
  # This should be the minimally required set for Docker.

  setting {
    namespace = "aws:ec2:vpc"
    name      = "VPCId"
    value     = "${var.vpc_id}"
  }
  setting {
    namespace = "aws:ec2:vpc"
    name      = "Subnets"
    value     = "${join(",", var.subnets)}"
  }
  setting {
    namespace = "aws:autoscaling:launchconfiguration"
    name      = "IamInstanceProfile"
    value     = "${var.instance_role}"
  }
  setting {
    namespace = "aws:elasticbeanstalk:environment"
    name      = "ServiceRole"
    value     = "${var.ebs_service_role}"
  }

}
</pre>

If you run this, at least one host and one ELB should appear in the defined subnets. Still, this is an _empty_ environment, there's no app running in it. If if you ask yourself, "where's the version he talked about?" - well, it's not in there. We didn't create one yet. This is just the very basic platform you need to run a version of an app.

In my source repo you can now just use the script <span class="lang:default decode:true crayon-inline ">app_config_create_and_upload.sh</span> , followed by <span class="lang:default decode:true crayon-inline ">deploy.sh</span> . You should be able to figure out how to use them, and they should work out of the box. But we're here to explain, so this is what happens behind the scenes if you do this:

  1. create a file "<span class="lang:default decode:true crayon-inline ">Dockerrun.aws.json</span> " with the information about the service (Docker image, etc.) to deploy
  2. upload that file into an S3 bucket, packed into a ZIP file (see "final notes" below)
  3. tell Elastic Beanstalk to create a new app version using the info from that file (on S3)

That obviously was <span class="lang:default decode:true crayon-inline ">app_config_create_and_upload.sh</span> . The next script, <span class="lang:default decode:true crayon-inline ">deploy.sh</span> , does this:

  1. tell EBS to actually deploy that configuration using the AWS cli.

This is the <span class="lang:default decode:true crayon-inline ">Dockerrun.aws.json</span>  file which describes our single-container test application:

<pre class="lang:default decode:true" title="Dockerrun.aws.json for version 'latest'">{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "flypenguin/test:latest",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": "5000"
    }
  ],
  "Volumes": [],
  "Logging": "/var/log/flypenguin-test"
}
</pre>

See "final notes" for the "ContainerPort" directive.

I also guess you know how to upload a file to S3, so I'll skip that. If not, look in the script. The Terraform declaration to add the version to Elastic Beanstalk looks like this: (if you used my script, a file called <span class="lang:default decode:true crayon-inline">app_version_<VERSION>.tf</span>  was created for you automatically with pretty much this content):

<pre class="lang:default decode:true" title="Terraform file to create version 'latest' in EBS"># define elastic beanstalk app version "latest"
resource "aws_elastic_beanstalk_application_version" "latest" {
  name        = "latest"
  application = "${aws_elastic_beanstalk_application.test_app.name}"
  description = "Version latest of app ${aws_elastic_beanstalk_application.test_app.name}"
  bucket      = "my-test-bucket-for-ebs"
  key         = "latest.zip"
}
</pre>

Finally, deploying this using the AWS cli:

<pre class="lang:sh decode:true " title="CLI command to deploy test-app's 'latest' version in environment 'test-env'">$ aws elasticbeanstalk update-environment \
  --application-name test-app \
  --version-label latest \
  --environment-name test-env 
</pre>

All done correctly, this should be it, and you should be able to access your app now under your configured address.

#### Wrap up & reasoning

My repo works, at least for me (I hope for you as well). I did not yet figure out the autoscaling, for which I didn't have time. I will catch up in a 2nd blog post once I figured that out. First tests gave pretty weird results 🙂 .

The reason _why_ I did this (when I have Rancher available for me) is the auto-scaling, and the host-management. I don't need to manage any more hosts and Docker versions and Rancher deployments just to deploy a super-simle, CPU-intensive, scaling _production_ workload, which relies on very stable (even pretty conservative) components in that way. Also I learned something.

Finally, after reading a lot of postings and way to much AWS docs, I am surprised how easy this thing actually is. It certainly doesnt look that way if you start reading up on it. I tried to catch the essence of the whole process in that blog post.

#### Final notes & troubleshooting

  1. I have no idea what the <span class="lang:default decode:true crayon-inline ">aws_elastic_beanstalk_configuration_template</span>  Terraform resource is for. I would like to understand it, but the documentation is rather ... sparse.
  2. The solution stack name has semantic meaning. You must set something that AWS understands. This can be found out by using the following command:  
    <span class="lang:default decode:true crayon-inline">$ aws elasticbeanstalk list-available-solution-stacks</span> **  
** ... or on the AWS documentation. Whatever is to your liking.
  3. If you don't specify a security group (<span class="lang:default decode:true crayon-inline ">aws:autoscaling:launchconfiguration</span>  - "<span class="lang:default decode:true crayon-inline ">SecurityGroups</span> ") one will be created for you automatically. That might not be convenient because this means that on "terraform destroy" this group might not be destroyed automatically. (which is just a guess, I didn't test this)
  4. The same goes for the auto scaling group scaling rules.
  5. When trying the **minimal example**, be extra careful **when you can't access the service after everything is there**. The standard settings seem to be: Same subnet for ELB and hosts (obviously), and public ELB (with public IPv4 address). Now, placing a public-facing ELB into an internal-only subnet does not work, right? 🙂
  6. **The ZIP file:** According to the docs you can only upload the JSON file (or the Dockerfile file if you build the container in the process) to S3. But the docs are not extremely clear, and Terraform did not mention this. So I am using ZIPs which works just fine.
  7. The **ContainerPort** is always the port the applications listens on _in the container_, it is _not_ the port which is opened to the outside. That always seems to be 80 (at least for single-container deployments)

#### Appendix I: Create ServiceRole IAM role

For some reason on the first test run this did not seem to be necessary. On all subsequent runs it was, though. This is the way to create this. Sorry that I couldn't figure out how to do this with Terraform.

  * open AWS IAM console
  * click "Create new role"
  * Step 1 - select role type: choose "AWS service role", and under that "AWS Elastic Beanstalk"
  * Step 2 - establish trust: is skipped by the wizard after this
  * Step 3 - Attach policy: Check both policies in the table (should be "AWSElasticBeanstalkEnhancedHealth", and "AWSElasticBeanstalkService")
  * Step 4 - Set role name and review: Enter a role name (e.g. "aws-elasticbeanstalk-service-role"), and hit "Create role"

Now you can use (if you chose that name) "aws-elasticbeanstalk-service-role" as your ServiceRole parameter.

#### Appendix II: Sources

  * [AWS Elastic Beanstalk architectural Overview][2] (reference)
  * [AWS Elastic Beanstalk configuration settings][3] (reference)
  * [Elastic Beanstalk Terraform setup][4]
  * [How to deploy Docker apps to Elastic Beanstalk][5]
  * [Deploying AWS Elastic Beanstalk applications with Terraform][6]

 [1]: https://github.com/flypenguin/code-misc/tree/master/blog/aws-elasticbeanstalk-terraform-demo
 [2]: http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.concepts.architecture.html
 [3]: http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-general.html
 [4]: https://github.com/RafalWilinski/elastic-beanstalk-terraform-setup
 [5]: https://github.com/hopsoft/relay/wiki/How-to-Deploy-Docker-apps-to-Elastic-Beanstalk
 [6]: https://is.gd/9Kj1sB