# ClojureScript and React

This book covers how to write native apps for both mobile devices and the desktop. 
We will be using: 

- [React Native](https://facebook.github.io/react-native/) for mobile devices
- [Electron](electron.atom.io) for the desktop

This book was written using the [Brackets editor](http://brackets.io/) by Adobe, an editor leveraging the same technology. A web application packaged as a native app for OS environments.


## React Native

React Native was developed by Facebook in order to:

- better leverage their web development skills
- allow for code reuse across multiple platforms

Traditionally you had to have a dedicated team of specialists for each platform, each running on a completely different development stack and runtime platform. You then had to rewrite each app from scratch suited to that particular stack and runtime environment, a complex and costly affair.

- iOS
- Android
- Windows (mobile)

## Electron

Electron was developed as an extension of Atom, to leverage the same technology for writing desktop apps. Electron includes a separate application packager: [electron-packager](), which packages the web app as a desktop app for various operating systems.
As of 2016 it supports the following environments:

- Mac OSX
- Unix/Linux
- Windows

## Atom

The Atom editor was developed by Github as a more lightweight but powerful development environment. It is developed using Electron.
The advantage of this approach is that the application becomes easily extensible by any web developer.

