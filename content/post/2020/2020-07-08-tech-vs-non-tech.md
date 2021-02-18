---
title: Tech vs. “Non-Tech”
author: penguin
type: post
date: -001-11-30T00:00:00+00:00
draft: true
url: /2020/07/08/tech-vs-non-tech
categories:
  - Longer things
tags:
  - current state

---
Did you ever wonder how far exactly the worlds of "tech industry" and non-tech-industry can be apart? No? Yes? Ha, don't care. Here it is.

But before I start  - what's "non-tech"? Pretty simple, the opposite of "tech". If we're talking about "tech" we usually mean all the products, companies, organizations, software, tools, what not related primary to "The Tech Sector (tm)".

So let's just look at the **"situation in tech"** ...

I remember when I started programming. I'm an average developer, pretty much out of practice. I know Python, and I knew Java and a bit of C/C++. When I started my first compiler ([Turbo Pascal][1]) came with 4 or 5 books (yes, really) which basically contained the "standard library".

Today, products like [Kafka][2], [Flink][3], [Spark][4], [K8S][5], [Traefik][6], [Consul][7], [Terraform][8], [PyTorch][9], [Numpy][9], [TensorFlow][9], [Vue][10], [Ember][11], [NodeJS][12], [React][13], [ReactNative][14], [Bootstrap][15], [Qt][16], [Redis][17], [Postgres][18], [Prometheus][19] are available as free software for everyone, and most of them use concepts which originated in concepts which themselves had not been invented back in the day. With a simple "pip install -r requirements.txt" you can pull in more functionality than you could ever _dream_ about writing. People are writing their own [non-euklidian 3d game engines][20] _for fun_. It's insane.

On the other side ... **may I present - The Healthare Industry**.

This is basically "let's get real". Every single thing of the following list I experienced personally. I'm "from tech" - so to me what follows now is a very bleak and frustrating statement of facts about the healthcare industry as a whole.

Let's get started.

  * One of our most critical systems uses a web server which had its latest release in 2002.
  * Containers are metal things you use for transporting real world cargo.
      * If not, the item "look at containers" is on their roadmap, "definitely". No specifics though, and no, sorry, "not in the near future".
  * CI/CD is a meaningless set of letters.
  * If you put a web based software (if you can find one) behind a load balancer you will face pure and utter confusion from the vendors.
      * You will even be warned of "unsupported scenarios".
  * If you're lucky you'll have an installation script or an installer.
      * If you're _very_ lucky it is not made from "copy" commands. Yes, that includes Windows.
      * A ZIP file with 17.000 files is a perfectly normal installation package. It's also "cross platform".
      * If you're on Linux - don't even think about requesting a .deb/.rpm package.
  * Nothing scales horizontally.
      * Scaling means "add 16 GB more RAM".
      * One "noble exception": Surprisingly, our most antique piece of software scales best. In theory is officially supports a HA concept based on DRBD and pacemaker. Of course it's not set up, because nobody dares to.
  * People are writing their own object serialization protocols, because "the ones coming with Java aren't good enough".
      * Then they load the whole database in system RAM, "to be fast". (Guess if they are).
      * Yes, if you wanted to scale this (which AFAIR is unsupported) you need that RAM on each node.
      * This is one of the better apps though. (Yes, really)
  * Everything needs downtimes for reboot (because nothing scales).
  * Cloud is "forbidden".
      * Of course, a standard installation on EC2 / Azure is (of course) "unsupported".
      * Also no one knows what "cloud" actually means, everybody thinks it's just "a computer somewhere else".
  * Informix databases, Oracle databases, _lots_ of SQLServer databases. Postgres? Nah. MySQL/Maria? Nah.
  * Everything is Windows-Software. If you have a pure Java application - it's still Windows. Even if Linux is supported - you still have to install it on Windows, because (no joke) the tesseract-based OCR is only supported on Windows.
  * Vendors support "linux installations", but have never actually done it. So you have to explain Linux concepts to the (paid) consultant who is sent to help you install the aforementioned ZIP archive containing 17.000 files
  * Your monitoring tool takes pride in being be able to "install multiple instances on one server", but their Docker container is "experimental".
  * Classical 3-tier-applications (client, server, database) require direct database access from the client, including - of course - the DB master passwords.
  * A PDF is "digital data". Like in the thing that is usually being sent to the printer.
      * Even a scan of a document is considered "digital data".
      * No, no one understands what "structured data" is. (Well, way too few people).
  * Every. Single. Installation. Is manually managed by the vendor or partner. There basically _is_ no documentation.
  * You need special software to save a read-only copy to somewhere.
      * That software is ridiculously expensive and utter crap, but it has a certification that says "read only archiving software".
      * No, it's not a "backup" software - it's a software to store read-only copies of documents for compliance purposes.
      * If that sounds like a read-only S3 bucket - yes, that's basically it. Please pay six figures.
  * CSV imports. Everybody is _very proud_ that their software can "easily import CSV data". In the worst case you just have to give them the schema, and they will (read: have to) do it for you. "No problem at all!"
      * Exports are ... trickier. (a.k.a "Well ... that is ... so ... you're the first person asking for this ...")
      * If you have import/export scenarios it's inevitablly placing files in directories.
      * If you feel a slight pain now thinking "oh my god that is going to be tricky using network file shares" - you are on point.
  * No APIs. Most of the vendors don't even understand the question.
      * And if they do, you probably have to pay for it. 5 figures, if you're lucky.
      * Don't ask about REST. Just don't.
  * Oh - one exception. One of our partners has "an API". He said. It looks like this:
      * A file is placed in a directory. (Side note: The file format documentation is 200+ pages of data tables without a single line of text explanation)
      * On a Windows machine. Using an RDP connection. Over a VPN. That file is then automatically picked up, and automatically transferred to ...
      * **the person responsible for performing the sanity check**. (He even get's an email on arrival - all automatically!!)
      * That person then fixes the file if necessary, and re-injects it into the processing pipeline. (No idea what comes after, to be honest).
      * See? API. (API = A Perfect Idiocy)
  * No logging.
      * Or if you have logging, it's like they invented a different logging scheme for every class that calls "log.info()".
  * If you buy a software, the vendor removes features you explicitly requested and paid for. Then you have to use the now crippled software, because that feature removal was "necessary".
      * And the feature just might be your user authentication, so nobody can use it any more.
      * You might also pay for a re-implementation, because "the architecture is now so much better - it's just the way it worked before is no longer possible." (That is still to be discussed as I understand it)
      * By the way, an apology is yet to be offered.
  * SAML and OpenID are considered weird niche phenomena which nobody uses. Most vendors don't even know what it is.
      * Even SSO using plain old AD is very rare. Everyting has its own password. Yay!!
  * Passwords have a max length. Of 8 chars, sometimes.
      * User identifiers as well.
  * Customer-specific configuration compiled into apps. So every customer, or even every customer site, gets its own app version.

Yeah.

The positive news? People are waking up. Veeeeeeery slowly, but they do. I mean, at some point you _have_ to admit you live and breathe in hell if everybody around you dies screaming, right?

And again - this is **the whole industry**, except some few, small players who slowly emerge from the sea of incompetence and vendor-lock-ins.

 [1]: https://en.wikipedia.org/wiki/Turbo_Pascal
 [2]: https://kafka.apache.org/
 [3]: https://flink.apache.org/
 [4]: https://spark.apache.org/
 [5]: https://kubernetes.io
 [6]: https://docs.traefik.io
 [7]: https://www.consul.io
 [8]: https://terraform.io
 [9]: https://www.tensorflow.org/
 [10]: https://vuejs.org
 [11]: https://emberjs.com/
 [12]: https://nodejs.org
 [13]: https://reactjs.org
 [14]: https://reactnative.dev
 [15]: https://getbootstrap.com
 [16]: https://qt.io
 [17]: https://redis.io
 [18]: https://postgresql.org
 [19]: https://prometheus.io
 [20]: https://www.youtube.com/watch?v=kEB11PQ9Eo8&list=PLChHnnPSNvIPznGRVYAE6Z-EcMnIYVhUQ&index=22&t=0s