---
title: Microsoft Code and Typescript
author: penguin
type: post
date: 2015-06-19T16:36:33+00:00
url: /2015/06/19/microsoft-code-and-typescript/
categories:
  - Uncategorized
tags:
  - dev
  - js
  - mscode
  - node
  - typescript

---
So being on the enterJS conference I wanted to get started with JavaScript. And TypeScript seems really promising. And once again I stumbled over the DIW pattern - Download, Install, Weird error messages.

So, here's my take on how to get started.

I wanted ...

  *  to try node-hid to see my USB HID devices, using TypeScript, Microsoft Visual Studio Code, and Node.js.

So here's how to get it to work:

<pre>mkdir my_project_dir
cd my_project_dir
brew install node            # I'm on Mac ;)
npm install -g typescript    # install globally
npm install node-hid         # install locally
</pre>

Fire up Code, and "open" the project directory "my\_project\_dir".

Press "CMD-SHIFT-P" (probably CTRL-SHIFT on non-OSX machines) and search for **"Configure task runner"**. Here you use this configuration:

<pre>{
  "version": "0.1.0",
  "command": "tsc",
  "isShellCommand": true,
  "showOutput": "silent",
  "args": [],
  "problemMatcher": "$tsc"
}
</pre>

Then create a new file called "test.ts", with the following content:

<pre>// typescript does not know "require".
// you have to convince it it "will exist at runtime".
// http://stackoverflow.com/a/12742371/902327
declare function require(name:string);
var HID = require('node-hid');
var devices = HID.devices();
console.log(devices);
</pre>

Now if you press "CMD-SHIFT-B" this should compile and create a file called "test.js".

I don't know (yet) how to run it from within Code, so on the command line I can test it by doing ...

<pre>$ node test.js
 { vendorId: 1452,
    productId: 589,
    path: 'USB_05ac_024d_fa120000',
    serialNumber: '',
    manufacturer: 'Apple Inc.',
    product: 'Apple Internal Keyboard / Trackpad',
    release: 521,
    interface: -1 },
  { vendorId: 0,
    productId: 0,
    path: '',
    serialNumber: '',
    manufacturer: '',
    product: 'Apple Mikey HID Driver',
    release: 0,
    interface: -1 },
  { vendorId: 1452,
    productId: 589,
    path: 'USB_05ac_024d_fa120000',
    serialNumber: '',
    manufacturer: 'Apple Inc.',
    product: 'Apple Internal Keyboard / Trackpad',
    release: 521,
    interface: -1 },
  { vendorId: 1452,
    productId: 589,
    path: 'USB_05ac_024d_fa120000',
    serialNumber: '',
    manufacturer: 'Apple Inc.',
    product: 'Apple Internal Keyboard / Trackpad',
    release: 521,
    interface: -1 },
  { vendorId: 1452,
    productId: 589,
    path: 'USB_05ac_024d_fa120000',
    serialNumber: '',
    manufacturer: 'Apple Inc.',
    product: 'Apple Internal Keyboard / Trackpad',
    release: 521,
    interface: -1 } ]</pre>

* * *

Article Sources:

[Microsoft Visual Studio Help][1]  
[Stackoverflow][2]

 [1]: http://blogs.msdn.com/b/typescript/archive/2015/04/30/using-typescript-in-visual-studio-code.aspx
 [2]: http://stackoverflow.com/a/12742371/902327