---
title: Firefox close tab buttons on mouse hover
author: penguin
type: post
date: 2018-09-26T18:59:04+00:00
url: /2018/09/26/firefox-close-tab-buttons-on-mouse-hover/
categories:
  - Uncategorized
tags:
  - firefox
  - snippet

---
I used to have Firefox's TabMix Plus addon installed. And the feature I missed most - surprisingly - is to have the "close tab" buttons appear on a tab when you hover the mouse over it.

Googling a little bit told me how to bring it back:

  * Create a [userChrome.css][1] file
    ($[FF\_PROFILE\_FOLDER][2]/chrome/userChrome.css)
  * Enter the code below (taken [from here][3], which is basically the original of this article)

```
.tabbrowser-tab:not([selected]):not([pinned]) .tab-close-button {
    visibility: hidden !important;
    margin-left: -16px !important;
}
.tabbrowser-tab:not([selected]):not([pinned]):hover .tab-close-button {
    visibility: visible !important;
    margin-left: 0px    !important;
    display: -moz-box   !important;
}
```

 [1]: http://kb.mozillazine.org/index.php?title=UserChrome.css
 [2]: http://kb.mozillazine.org/Profile_folder#Folders
 [3]: https://mattsch.com/2016/01/04/firefox-close-tab-button-on-hover/