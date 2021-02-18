---
title: Docker registry, S3 and permissions
author: penguin
type: post
date: 2015-11-26T16:06:57+00:00
url: /2015/11/26/docker-registry-s3-and-permissions/
categories:
  - Docker
tags:
  - aws
  - s3
  - solution

---
There are a couple of bazillion blog posts saying "yah just did my docker registry on S3".

It's not so easy, though. Cause what if you want to limit access to a certain IAM user? Yup, you need to go deep (well, a bit) into the policy thing of Amazon. Which sounds simple, but isn't.

I got "HTTP 500" errors from the docker registry when I first deployed. My configuration, which was **wrong**, looked like this:

```
"RegistryIAMUser" : {
  "Type" : "AWS::IAM::User"
},
"RegistryIAMUserAccessKey" : {
  "Type" : "AWS::IAM::AccessKey",
  "Properties" : { "UserName" : { "Ref" : "RegistryIAMUser" } }
},
"Bucket" : {
  "Type" : "AWS::S3::Bucket",
  "Properties" : { "BucketName" : "flypenguin.docker-registry" }
},

"RegistryPrivateAccess" : {
  "Type" : "AWS::S3::BucketPolicy",
  "Properties" : {
    "Bucket" : {"Ref":"Bucket"},
    "PolicyDocument": {
      "Statement":[{
        "Action":[ "s3:*" ],
        "Effect":"Allow",
        "Resource":  { "Fn::Join" : ["", ["arn:aws:s3:::", { "Ref" : "Bucket" } , "/*" ]]},
        "Principal": {"AWS" : {"Fn::GetAtt":["RegistryIAMUser","Arn"]}}
      }]
    }
  }
}
```

Since this didn't work really well, I googled my a** off and found a little post, which used a UserPolicy (instead of a bucket policy, which is basically the other way around), but did _one_ thing different. My **working** configuration is now ... (let's see if you can see the difference):

```
[... same as above ...]

"UserPolicyRegistryPrivateAccess" : {
  "Type" : "AWS::IAM::Policy",
  "Properties" : {
    "PolicyName" : "AccessToDockerBucket",
    "Users" : [ {"Ref":"RegistryIAMUser"}],
    "PolicyDocument" : {
      "Version" : "2012-10-17",
      "Statement" : [{
        "Effect":"Allow",
        "Action":[ "s3:*" ],
        "Resource": [
          { "Fn::Join" : ["", ["arn:aws:s3:::", {"Ref":"Bucket"} , "/*" ]]},
          { "Fn::Join" : ["", ["arn:aws:s3:::", {"Ref":"Bucket"} ]]}
        ]
      }]
    }
  }
}
```

See it?

It's the _two_ resources now. You need not only "resource/*" as a target, you _also_ need "resource" _itself_ as a target. Which makes sense if you know it and think about it. If you don't ... it's a bit annoying. And time-consuming.