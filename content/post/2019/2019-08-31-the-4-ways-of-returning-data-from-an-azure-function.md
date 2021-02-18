---
title: The 6 ways of returning data from an Azure Function
author: penguin
type: post
date: 2019-08-31T12:22:33+00:00
url: /2019/08/31/the-4-ways-of-returning-data-from-an-azure-function/
categories:
  - Cloud
tags:
  - azure
  - functions
  - javascript
  - serverless

---
Why have one if you can have many? Well, beats me, but I thought I'd collect them here so I have my personal reference. **Disclaimer:** This is only for JavaScript based functions (most of the examples on the MS pages are C#).

So, most of them have to do with the file `function.json`  file:

```
{
    "bindings": [{
        "type": "httpTrigger",
        "name": "req",
        "and": "so on ..."
    }, {
        "type": "http",
        "name": "thisIsAnOutputBinding",
        "direction": "out"
    }, {
        "type": "someOtherType",
        "name": "anotherBinding",
        "direction": "out"
    }]
}
```

More precisely, with the "out" type binding that is defined in the 2nd "bindings" object. The **"out" binding has a "name" property,** which is basically relevant for all of the methods.

**!!!!! WARNING !!!!! - It seems the name of the binding property must be <code class="EnlighterJSRAW" data-enlighter-language="null">camelCase</code>****. At least I consistently get "invalid binding" errors when I use <code class="EnlighterJSRAW" data-enlighter-language="null">snake_case</code>**

# Method 1 - context property

The context object has one property per defined "out" binding. So if our property is named "thisIsAnOutputBinding", our result property is `context.`thisIsAnOutputBinding , or as MicroSoft puts it:

> Outputs (bindings of `direction === "out"` ) can be written to by a function in a number of ways. In all cases, the `name` property of the binding as defined in _function.json_ corresponds to the name of the object member written to in your function.

Simply put, you just assign the value to a `context`  member and you're done:

```
module.exports = function(context, req) {
    context.thisIsAnOutputBinding = {
        "my": "return value",
        "in this case": "an object"
    };
    // contect.done() is only needed for synchronous functions context.done();
}
```

[Source here][1].

# Method 2 - return an object (async functions only)

With the bindings given above, you can assign the values to the context property, but you can also return an object whose keys correspond to the binding names:

```
module.exports = async function(context) {
  return { thisIsAnOutputBinding: 42, anotherBinding: 43 };
  // of course, instead of 42/43 you can put any js object here.
};
```

&nbsp;

[Source here][1].

# Method 3 - using context.bindings

This is basically a variant of method 1. Why does it exist? No one knows. Apparently the context object has a `.bindings`  property, which in turn again has properties which name-match the defined out bindings. So this is another possibility, and I think the `return`  is unnecessary:

```
module.exports = async function(context) {
  let retMsg = "Hello, world!";
  context.bindings.thisIsAnOutputBinding = {
    body: retMsg
  };
  context.bindings.anotherBinding = retMsg;
  return;
};
```

[Source here][1].

# Method 4 - using context.done()

If you're using a sync method, you can't return an object, but you can call `context.done(err, obj)` .

```
module.exports = async function(context) {
  context.done(null, {
    thisIsAnOutputBinding: { text: "hello there, world", anotherBinding: 42 }
  });
};
```

[Source here][2].

# Method 5 - just return it already (only async functions)

Probably someone said "Well, if I only have one output binding, why should I explicitly address it?", which is a valid thought. So another way to return data was invented.

For this the configuration in function.json looks a bit different:

```
{
  "bindings": [
    { "type": "someType", name: "req", direction: "in" },
    { "type": "someOutType", direction: "out", name: "$return" }
  ]
}
```

You have to set exactly one "out" type binding, and the name must be "$return". Then you can do this:

```
module.exports = async function(context, req) {
  return "woohooo!!";
};
```

[Source here][3].

# Methods 6 - special for http outputs

You thought we were done? Noooooo. For http methods, you can diretly return the body object which is then used to create the body

```
// actually NO IDEA if this works sync, async or both
// my guess is: sync, because "context.done() is implicitly called"
module.exports = async function(context, req) {
  rv = { body: "&lt;html/&gt;", status: 201 };
  context.res.send(rv);
};
```

[Source here][4].

# Summary

Let's just say it's a mess, and too many ways ruin the map.

It works though.

 [1]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#outputs
 [2]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#contextdone-method
 [3]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#exporting-an-async-function
 [4]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#accessing-the-request-and-response