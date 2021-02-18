---
title: Install flutter on MacOS
author: penguin
type: post
date: 2020-07-08T21:43:22+00:00
url: /2020/07/08/install-flutter-on-macos/
categories:
  - Development
tags:
  - flutter
  - osx

---
I blog so I don't forget ... this:

First, the non-CLI actions:

  * install XCode from the App Store
  * install Flutter plugin for Visual Studio Code (if you haven't installed VS Code, do it now, you can also use "brew" for that)
  * download the latest Flutter SDK, unzip it, and move the unzipped <code class="EnlighterJSRAW" data-enlighter-language="generic">flutter/ </code>folder to <code class="EnlighterJSRAW" data-enlighter-language="generic">$HOME/Dev/frameworks</code> (or any other location you favor)
  * add <code class="EnlighterJSRAW" data-enlighter-language="generic">$HOME/Dev/flutter/bin</code> and <code class="EnlighterJSRAW" data-enlighter-language="generic">$HOME/Dev/flutter/bin/.pub-cache/bin</code> to your path

That's about it for now. Then do some CLI magic:

```
brew tap dart-lang/dart
brew install dart android-studio android-sdk
sudo gem install cocoapods
pod setup
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
flutter doctor --android-licenses
```

After that some more UI stuff:

  * Start Android Studio. It will want to install a couple of things (Intel drivers pop up, weirdly)

And finally: A final <code class="EnlighterJSRAW" data-enlighter-language="generic">flutter doctor</code> will tell you if everything is all right.

The whole process will download about 10-12 GB of data.