# JDK Installation

Download the latest [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) from Oracle.

The official installation guide can be found:
- [Windows](http://docs.oracle.com/javase/7/docs/webnotes/install/windows/jdk-installation-windows.html). 
- [Linux](http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html)
- [Mac OSX](http://docs.oracle.com/javase/7/docs/webnotes/install/mac/mac-jdk.html)

Note: It should be the same procedure for Java 8 and beyond.

## Adding java to your PATH

In order for your system to find Java, you need to add it to your system `PATH` environment variable.

### Mac and Unix

- Open a Terminal or command line window
- Open the `~/.bash_profile` using a command-line editor.
- Set your `PATH` to include the JDK sub-folder named java. For example: `export PATH=$PATH:/usr/bin/java`

Your system should now be able to find the java executable by iterating through the folders of your PATH.

```bash
$ which java
/usr/bin/java
```

Success!!

### Windows

Determine the location of your JDK bin. Typically, this will be `C:\Program Files\Java` but may be located elsewhere. 
Once you have the JDK installation path:

- right-click *My Computer* and select *Properties* from the context menu
- In the *System Properties* box, select the *Advanced tab*
- Now click the *Environment Variables*
- Create a new variable called `JAVA_HOME` and set it to the location of your JDK root folder, such as `C:\Program Files\Java\`
- Find the `Path` row under *System Variables* and click *Edit*
- In the *Edit System Variable* box, ensuring that a semi-colon preserves the last path, append the path for the JDK `bin`. For example, `$JAVA_HOME\bin`
- Click *OK*
- Once you're back in the *System Properties, Advanced tab*, click *Apply Changes*


### Check Java installation

This applies for any Operating System.

```bash
$ java -version
java version "1.8.0_71"
Java(TM) SE Runtime Environment (build 1.8.0_71-b15)
```

All good :)
