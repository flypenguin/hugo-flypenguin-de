---
title: SARS-CoV-2, a.k.a “Corona Virus”, pseudo “data science” and Twitter
author: penguin
type: post
date: 2020-03-29T17:35:45+00:00
url: /2020/03/29/sars-cov-2-a-k-a-corona-virus-pseudo-data-science-and-twitter/
categories:
  - Data Science
tags:
  - heroku
  - pandas
  - python
  - twitter

---
Some terminology (taken from - who would have thought it - [t-online.de][1]) and background information:

  * **SARS-CoV-2** is the correct name of the virus. Everybody calls him "the Corona Virus" though, which is technically incorrect, cause there's only the _family_ of corona viruses - it's a group, not a single one.
  * **COVID-19** is the name of the disease. It's an acronym made of "**CO**rona **VI**rus **D**isease **19**". (It's basically the same as HIV and AIDS - one is the infection, the other the actual illness).
  * **It's not the flu**, but I think everybody has that down by now
  * It's **R0-value** (how many people are infected by one person on average) **is about 3** (source: [RKI Germany][2])

All of us are following the development of this very closely, cause we have no choice since we're all basically locked at home. And even if we would go out everything's closed. That gives us a ton of time to play DOOM Eternal (me), or write [twitter bots who regularly publish the numbers of COVID-19 infections in Germany][3] (me as well). As macabre as it is, this is very good oppotunity to start fooling around with data science. But to do this you need datasets, which are surprisingly hard to get. For the current case numbers  I found those:

  * The [The Humanitarian Data Exchange][4] has a [dataset of the current infections world wide][5] you can use
  * The [Berliner Morgenpost][6] also has a [nice page showing infection rate over time][7].

There's probably more, but like I said - it's surprisingly hard to find regularly updated, publicly available data sets in machine-readable form. (Also, this is the first time I go looking for this stuff, so maybe I just have no clue).

Now back to the twitter bot. What does it do exactly? [Go look for yourself][3], but if you are too lazy:

  * It prints the current infection rate at 8h, 12h, 16h, 20h. At the last time it will include a graph, a 1-week-forecast and a small evaluation how are stand today in comparison to the forecast which would have been made a week ago.
  * A friend helped me greatly by providing first a [Holt-Winters][8] prediction (which is live now), and then upgraded this to a more intelligent [ARIMA prediction][9], which is still buried in a jupyter notebook and waiting for daylight. (Maybe she will write a guest post here to explain what it does and how it works - but I haven't asked yet).
  * As for the bot's code - it's not (yet) public. Which is unusual for me, really. I should remedy that.

What I learned so far:

  * It's surprsingly easy and hard at the same time to get a [Twitter developer account][10]
  * Twitter does not permit publishing the exact same tweet multiple times in a short period of time
  * [Heroku][11] is really really nice for this, as long as you don't need to pay for it. I would be interested in alternatives.
  * [matplotlib][12] is a _lot_ more complicated than I expected 
      * but strangely neither bokeh, nor plotly can actually export png graphics without either a separate electron app (WTF?) or a headless browser and selenium (W-T-F?!?)
  * [pandas][13] rocks, or more precisely: pandas data frames rock.
  * there does not seem to be a single properly maintained [Twitter library][14] for JavaScript, but there is at least [tweepy][15] for Python. (I mean I _want_ to try something in JS, but honestly, if everything I find is outdated, I just stay with good old Python ...)
  * The infection growth is intensely exponetial - almost a straight line on a log scale plot.

Let's see where this goes, and I hope you all stay healthy.

 [1]: https://www.t-online.de/gesundheit/krankheiten-symptome/id_87524194/coronavirus-glossar-zu-wichtigen-begriffen-rund-um-corona.html
 [2]: https://www.rki.de/DE/Content/InfAZ/N/Neuartiges_Coronavirus/Steckbrief.html
 [3]: https://twitter.com/covidcounter_de/
 [4]: https://data.humdata.org/
 [5]: https://data.humdata.org/dataset/5dff64bc-a671-48da-aa87-2ca40d7abf02
 [6]: https://morgenpost.de
 [7]: https://interaktiv.morgenpost.de/corona-virus-karte-infektionen-deutschland-weltweit/
 [8]: https://en.wikipedia.org/wiki/Exponential_smoothing
 [9]: https://en.wikipedia.org/wiki/Autoregressive_integrated_moving_average
 [10]: https://developer.twitter.com/en
 [11]: https://heroku.com
 [12]: https://matplotlib.org
 [13]: http://pandas.pydata.org/
 [14]: https://developer.twitter.com/en/docs/developer-utilities/twitter-libraries
 [15]: https://www.tweepy.org/