---
title: Helm in a kops cluster with RBAC
author: penguin
type: post
date: 2018-05-25T09:50:56+00:00
url: /2018/05/25/helm-in-a-kops-cluster-with-rbac/
categories:
  - Infrastructure
  - Snippets
tags:
  - helm
  - kubernetes
  - rbac

---
I created a K8S cluster on AWS with kops.

I ran <span class="lang:default decode:true crayon-inline ">helm init</span> to install tiller in the cluster.

I ran <span class="lang:default decode:true crayon-inline ">helm list</span>  to see if it worked.

I got this:

<pre class="EnlighterJSRAW" data-enlighter-language="null">Error: configmaps is forbidden: User "system:serviceaccount:kube-system:default" \ 
    cannot list configmaps in the namespace "kube-system"</pre>

That sucked. And google proved ... reluctant. What I could figure out is:

### Causes

  * kops sets up the **cluster with RBAC** enabled (which is good)
  * helm (well, **tiller**) uses a standard role for doing things (which might be ok, at least it was with my stackpoint cluster), but in that case (for whatever reason) it **did not have sufficient privileges**
  * so we need to **prepare some cluster admin roles for helm** to use

### Fixes

Just do exactly as it [says in the helm docs][1] 🙂 :

  * apply the RBAC yaml file which creates the <span class="lang:default decode:true crayon-inline">kube-system/tiller</span> service account, and binds this to the <span class="lang:default decode:true crayon-inline ">cluster-admin</span>  role.
  * install helm with: <span class="lang:default decode:true crayon-inline ">helm init -service-account tiller</span>

Is that secure? Not so much. With helm you can still do anything to the cluster at all. I might get to this in a later post.

 [1]: https://github.com/kubernetes/helm/blob/master/docs/rbac.md#example-service-account-with-cluster-admin-role