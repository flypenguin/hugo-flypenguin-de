---
title: 'Azure App Service & Python, Part I'
author: penguin
type: post
date: 2020-11-19T22:55:11+00:00
url: /2020/11/19/azure-app-service-python-part-i/
categories:
  - Cloud
  - Infrastructure
tags:
  - azure
  - rant
  - serverless

---
A.k.a. "you've got to be fucking kidding me". Azure App Service sucks, as basically _any_ part of Azure. What's surprising this time is that [terraform][1] actually contributes to the sucking on this occasion - usually the Hashi folks do a very good job at reducing the annoyance. But maybe they've given up, I wouldn't blame them.

So what happened?

What confused the sh*t out of me the last days is that I see tutorials (all really, really, really bad) that _do_ use Python versions newer than 3.4 (not surprisingly). But, [alas, there's this][2]: the <code class="EnlighterJSRAW" data-enlighter-language="generic">site_config.python_version</code> field of terraform's <code class="EnlighterJSRAW" data-enlighter-language="generic">azurerm_app_service</code>resource. It allows the values "2.7" and "3.4" (a Python version which eol'ed in March '19).

After a shit ton of googling [I found this, though][3]. Turns out, **it's not even the right field to use!!** To select the Python runtime you should use <code class="EnlighterJSRAW" data-enlighter-language="generic">linux_fx_version</code>, which nobody, nowhere, ever documented. And naive me just assumed that something with "python" in its name would be the right thing to use. Stupid, I know, we're talking Azure here ...

 [1]: https://terraform.io
 [2]: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#python_version
 [3]: https://github.com/terraform-providers/terraform-provider-azurerm/issues/7010#issuecomment-639032626