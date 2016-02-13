# React Native Environment setup

## iOS development

For React Native development on Mac OS X, we have the following pre-requisites:

## Android development

The [android setup](https://facebook.github.io/react-native/docs/android-setup.html) 

Download the latest [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) from Oracle.

## Java Installation

### Mac OSX or Unix/Linux

- Open a Terminal or command line window
- Open the `~/.bash_profile` using a command-line editor.
- Set your `PATH` to include the JDK sub-folder named java. For example: `export PATH=$PATH:/usr/bin/java`

```bash
$ which java
/usr/bin/java
```

Check the Java version installed

```bash
$ java -version
java version "1.8.0_71"
Java(TM) SE Runtime Environment (build 1.8.0_71-b15)
```

### Windows

First, determine the location of your JDK bin. Typically, this will be `C:\Program Files\Java` but may be located elsewhere. 
Once you have the JDK installation path:

- right-click *My Computer* and select *Properties* from the context menu
- In the *System Properties* box, select the *Advanced tab*
- Now click the *Environment Variables*
- Create a new variable called `JAVA_HOME` and set it to the location of your JDK root folder, such as `C:\Program Files\Java\`
- Find the `Path` row under *System Variables* and click *Edit*
- In the *Edit System Variable* box, ensuring that a semi-colon preserves the last path, append the path for the JDK `bin`. For example, `$JAVA_HOME\bin`
- Click *OK*
- Once you're back in the *System Properties, Advanced tab*, click *Apply Changes*

