---
title: 'GitLab, App Service & CI/CD – Variant 1'
author: penguin
type: post
date: 2020-11-21T11:34:01+00:00
url: /2020/11/21/gitlab-app-service-ci-cd-variant-1/
categories:
  - CICD
  - Cloud
  - Infrastructure
tags:
  - azure
  - cicd

---
**[Updates]**

  * 2020-11-29 - reference terraform simple-appservice module
  * 2020-11-29 - add small fix for GitLab CI script

Since I spent _way_ more time fighting with this I though I might write a proper recap and maybe help others to get started. I also set up a [repository with example code][1] which should (!) be a "pull-and-run" thing once you have your ARM\_ACCESS\_KEY ready, which goes along with this blog post.

Now, let's get started. What we want to do here is basically pretty simple, and detailed in the picture below:

<div class="wp-block-image is-style-default">
  <figure class="aligncenter"><img loading="lazy" width="1024" height="667" src="https://flypenguin.de/wp-content/uploads/2020/11/gitlab-appservice-cicd-variant1-1024x667.png" alt="" class="wp-image-1281" srcset="https://flypenguin.de/wp-content/uploads/2020/11/gitlab-appservice-cicd-variant1-1024x667.png 1024w, https://flypenguin.de/wp-content/uploads/2020/11/gitlab-appservice-cicd-variant1-300x195.png 300w, https://flypenguin.de/wp-content/uploads/2020/11/gitlab-appservice-cicd-variant1-768x500.png 768w, https://flypenguin.de/wp-content/uploads/2020/11/gitlab-appservice-cicd-variant1.png 1042w" sizes="(max-width: 1024px) 100vw, 1024px" /></figure>
</div>

This sounds easy, right? It is, if you are done fighting Azure stupidity and know a couple of things. So what we need to do for this? We need to ...

  * **Create the App Service** (I use terraform for this, and you can use [my example repo][1] or my [terraform simple-appservice module][2] to get started)
  * **Create a GitLab repo** (of course, that's what this is all about, right? An example "hello world" flask up can also be found in the mentioned repo)
  * **Configure the GitLab CI** system to contain some credentials for the App Service
  * **Add a <code class="EnlighterJSRAW" data-enlighter-language="generic">.gitlab-ci.yml</code>file to your GitLab repo** to enable CI
  * ... done.

That should be exactly all. Let's get started.

## Create the app service

As said - please use my example repo and terraform. That should be enough:

  * make init
  * make plan
  * make do

Important to know:

  * use _at least_ "B1 | Basic" for your SKU settings
  * your "source_control" setting should be "LocalGit" I guess. There is an error in either Terraform or Azure (I think the latter) if you use "ExternalGit", which would be "variant 2" (a blog post to follow)

## Create a GitLab repo

[This one should do][3], and if you are rightfully annoyed by Azure give Render a try, they look cool (just found them about 2 mintes ago). To fork the repo you have to click the very unobtrusive "fork" button in the top right.

## Configure GitLab CI

The **only thing to do is set two environment variables** for the test runner. The information you need can be found either from my terraform output, or fom the Azure portal (images below). Then you add two variables in GitLab CI (GitLab repo -> settings -> CI/CD -> Variables):

  * **AZ\_APP\_NAME** (in this case this would be "flypenguin-coolapp-xrp")
  * **AP\_APP\_REPO_PASS** (in this case this would be "SYut....") [Best\_Wordpress\_Gallery id="2&#8243; gal_title="2020-11-21 cicd app service gitlab credentials"] 

There are again a few caveats:

  * Basically the whole thing is based on the app name. Your user name should be your app name prefixed by a "$" sign, which is **highly annoying**.
  * If you ever want to use a "$" sign in any GitLab runner environment variable, you have to escape it with another "$".  
    Example: "my$variable" should be "my<span style="text-decoration: underline;"><strong>$$</strong></span>variable" in the "value" field of GitLab.

## Add .gitlab-ci.yml file to repo

Well, just add the file and push the repo.

<pre class="brush: yaml; title: ; quick-code: false; notranslate" title="">stages:
  - push_to_azure
push_it:
  stage: push_to_azure
  only:
    - master
  allow_failure: false
  before_script:
    - git config --global user.email "some@email.address"
    - git config --global user.name "GitLab CI Pipeline"
  script:
    - export REPO_FQDN="$AZ_APP_NAME.scm.azurewebsites.net"
    - export REPO_URL="https://\$$AZ_APP_NAME:$AZ_APP_REPO_PASS@$REPO_FQDN/$AZ_APP_NAME.git"
    - git remote add azure_app_service "$REPO_URL"
    - git remote -v
    # DEEP DIVE INFORMATION, DELETE ME IF YOU WANT
    # the local branch is 'detached head' - which is fucked.
    # we can't do "git push --force azure HEAD:master" on the FIRST push.
    # we can't push into an empty repository at all, even when using
    #   git push --force azure HEAD:refs/heads/master
    # because on the first push this will still not work.
    # the actually easiest way seems to be to not do "detached head" here.
    # so let's try to "unshallow" that thing.
    # https://is.gd/t7y7LM / https://is.gd/8YE4Ua
    # https://is.gd/rZDwxy / https://is.gd/rZDwxy
    - git fetch --unshallow origin || true
    - git push --force azure_app_service HEAD:refs/heads/master
</pre>

## Finally - test.

Just push a change to your repo and see if it works. The first obvious change is the addition of the <code class="EnlighterJSRAW" data-enlighter-language="generic">.gitlab-ci.yml</code> file ... .

Hope that helped, hope it works 🙂

 [1]: https://github.com/flypenguin/ex-appservice-cicd-with-terraform
 [2]: https://registry.terraform.io/modules/flypenguin/simple-appservice/azure/latest
 [3]: https://gitlab.com/render-examples/flask-hello-world