---
title: 'Sublime Text: Keyboard Shortcut for setting Tab Width'
author: penguin
type: post
date: 2014-08-20T17:50:14+00:00
url: /2014/08/20/sublime-text-keyboard-shortcut-for-setting-tab-width/
categories:
  - Uncategorized

---
Schwer zu finden ...  
Wer schnell über das Keyboard die Tab Width verändern will, der tue das so:  
`<br />
   {<br />
        "keys": ["super+k", "super+2"],<br />
        "command": "set_setting",<br />
        "args":<br />
        {<br />
            "setting": "tab_size",<br />
            "value": 2<br />
        }<br />
    }<br />
` 

(gefunden hier: http://is.gd/Pz9zE0)