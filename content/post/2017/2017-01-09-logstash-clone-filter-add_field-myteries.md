---
title: 'Logstash, clone filter & add_field mysteries'
author: penguin
type: post
date: 2017-01-09T10:49:29+00:00
url: /2017/01/09/logstash-clone-filter-add_field-myteries/
categories:
  - Snippets
tags:
  - annoying
  - logstash

---
[That's][1] a really great piece of documentation. [This][2] **does not work**:

<pre class="lang:default highlight:0 decode:true"># let's clone each event, one goes to somewhere, one goes to somewhere else.
# note this was copy-pasted from the docs!
# see here: https://is.gd/QSHNps
# again. THIS DOES NOT WORK.
filter {
  clone {
    add_field =&gt; { "token" =&gt; "ABCDEF" }
  }
}
output {
  if [token] {
    # go somewhere
    tcp { ... }
  } else {
    # go to somewhere else
    s3 { ... }
  }
}</pre>

Why? Because **the clone filter will not clone anything.** And the documentation is super unclear on this. If you know it, you can read it - if you don't know this, you'll ... google.

For it to actually clone anything you have to specify the 'clones => ["one", ...]' parameter. _Then_ it will clone, and add the token field as expected. Like this:

<pre class="lang:default highlight:0 decode:true ">filter {
  clone {
    clones =&gt; ["logz"]     # NOW it will clone.
    add_field =&gt; { "token" =&gt; "ABCDEF" }
  }
}</pre>

Interestingly the "clones =>" [parameter is _optional_][3], which just confuses the shit out of me.

The reasoning that I don't just add the field altogether is that this is the access token for our externally hosted ELK service. This should _only_ be there for the external path, and not be put in S3 in parallel.

###### References:

  * https://logstash.jira.com/browse/LOGSTASH-2144

 [1]: https://www.elastic.co/guide/en/logstash/current/plugins-filters-clone.html
 [2]: https://is.gd/QSHNps
 [3]: https://is.gd/tT2pTG