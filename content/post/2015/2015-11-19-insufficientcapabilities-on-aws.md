---
title: InsufficientCapabilities on AWS
author: penguin
type: post
date: 2015-11-19T09:50:29+00:00
url: /2015/11/19/insufficientcapabilities-on-aws/
categories:
  - Uncategorized

---
New project. I can play around as much as I want, as long as on day X I am done.

Really frightening, and really cool.

Anyway, first operation: Create a bunch of S3 buckets and IAM roles to interface with them. Which is kinda not-so-easy.

Beacause when you create IAM capabilities with cloudformation, you get this error:

```
{
    "CapabilitiesReason": "The following resource(s) require capabilities: [AWS::IAM::AccessKey, AWS::IAM::User]", 
    "Capabilities": [
        "CAPABILITY_IAM"
    ], 
    "Parameters": []
}
```

... which is a fancy way of saying "do this":

```
# aws cloudformation create-stack \
    --template-url file://env.json 
    --capabilities CAPABILITY_IAM
```

... which you don't really find easily with google. Or everybody knows, but me. Gnaah.