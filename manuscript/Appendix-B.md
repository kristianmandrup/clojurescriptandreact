## Android development

The [android setup](https://facebook.github.io/react-native/docs/android-setup.html) 

### Mac Android dev

Install command-line tools for [Xcode](https://developer.apple.com/xcode/) via Terminal.

`xcode-select –install`

Install [Homebrew](http://brew.sh/)

```bash
export BREW_INSTALL=https://raw.githubusercontent.com/Homebrew/install/master/install`
/usr/bin/ruby -e "$(curl -fsSL $BREW_INSTALL)"
```

Now install Android SDK via Homebrew:

`$ brew install android-sdk`

Awesome!!

### Windows Android dev

- [Download Android SDK](https://developer.android.com/sdk/installing/index.html)
- Install it

### Configure Android SDK

Edit `~/.bash_profile` and export the `ANDROID_HOME` system variable.

`export ANDROID_HOME=<path_where_you_unpacked_android_sdk>`

On Mac OSX this would typically be:

```bash
# If you installed the SDK via Homebrew, otherwise ~/Library/Android/sdk
export ANDROID_HOME=/usr/local/opt/android-sdk
```

On Windows, go to `Control Panel -> System and Security -> System -> Change settings -> Advanced -> Environment variables -> New`
and add a similar variable.

### Gradle

React Native Android use [gradle](https://docs.gradle.org/) as a build system. We recommend to *enable gradle daemon* which may result in up to *50% improvement* in incremental *build times for changes in java code*. 

Add the line: `org.gradle.daemon=true` to the file `$USER_HOME/.gradle/gradle.properties`, where `$USER_HOME` is your home directory. Typically one of the following:

```bash
C:\Users\<username> (Windows Vista & 7+)
/Users/<username> (Mac OS X)
/home/<username> (Linux)
```

Open the *Android SDK Manager* (on Mac start a new shell and run `android`)
In the window that appears make sure you check:
- Android SDK Build-tools version 23.0.1
- Android 6.0 (API 23)
- Android Support Repository

Click *Install Packages*

### Install Genymotion Android simulator

- [Download and install Genymotion](https://www.genymotion.com/).
- Open Genymotion. 
- Create a new emulator and start it.
- To bring up the developer menu press `⌘+M`

Note that Genymotion might ask you to install [VirtualBox](https://www.virtualbox.org/) unless you already have it.


