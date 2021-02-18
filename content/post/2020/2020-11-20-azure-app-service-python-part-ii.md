---
title: 'Azure App Service & Python, Part II'
author: penguin
type: post
date: 2020-11-20T16:39:42+00:00
url: /2020/11/20/azure-app-service-python-part-ii/
categories:
  - Cloud
tags:
  - azure
  - rant
  - serverless

---
God I _hate_ Microsoft.

**TL;DR** - use "Basic" and "B1" as your app service SKUs. Anything "below" won't work and you won't know why.

So, why I am again angry? Cause I want to deploy an app service, using terraform. Sounds soooooooooo simple, right? Except I fail at the creation of the app service (even without code, yet).

Example:

```
resource "azurerm_app_service_plan" "plan" {
  name                = "pkd-appservices0"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  kind                = "Linux"
  reserved            = true

  sku {
    tier = "Dynamic"     # WRONG, use at least "Basic"
    size = "Y1"          # WRONG, use at least "B1"
  }
}
```

```
resource "azurerm_app_service" "appservice" {
  name                = random_string.server.result
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.plan.id

  site_config {
    scm_type         = "LocalGit"
    linux_fx_version = "PYTHON|3.8"
  }
}
```

Now. What happens? Let's see:

```
Error creating App Service "wzkytawt" (Resource Group "pkd-it-appservices0"):
   web.AppsClient#CreateOrUpdate: Failure sending request: StatusCode=0 --
   Original Error: autorest/azure: Service returned an error. Status=<nil> <nil>
```

What? Okay, our app service fails without any indication. By setting "<code class="EnlighterJSRAW" data-enlighter-language="generic">TF_LOG=TRACE</code>" and reading through 100s of lines of code we get this:

```
Consumption pricing tier cannot be used for regular web apps
```

What the fuck is a "consumption pricing tier"? Well, turns out MS doesn't know as well, at least it's described exactly nowhere. Also, **can you get a list of tiers?** No! Of _course_ not! Why would you?  [Stackoverflow to the rescue][1], again, btw, and no idea where that guy pulled that priceless list from.

So, set things to "F1 | Free" and everything works.

... or not. Now we get this:

```
There was a conflict.                                              (really?!)

64 Bit worker processes cannot be used for the site as the plan
does not allow it.                                                 (WTF?)

For more information on pricing and features, please see:
https://aka.ms/appservicepricingdetails                            (oh! a link! NICE)
```

Formatting and comments by me ... . Now, wondering why the fuck a multi-billion-dollar corp can do less than my Raspi (run 64bit code) I head over to said page. And **find nothing**. There is not a single mention about 64bit, 32bit, or anything helpful. Just ... prices.

Assuming the fuckers just want more money I upgrade SKU to "B1 | Basic", and everything works. (Update: I just saw that this is actually [mentioned in the terraform docs][2] 🙂

This time for real, and I think I might be onto the path of finally understanding everything Microsoft does.

 [1]: https://stackoverflow.com/a/47522889
 [2]: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#use_32_bit_worker_process