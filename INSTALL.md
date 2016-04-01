# Dependencies

Trusty uses **ivy** for dependency management and **ant** to build / test / run. You need to install both to use this project.
For example with ubuntu, do
```BASH
apt-get install ant ivy
```
and you are good to go. Otherwise, use the corresponding manuals for [ivy](http://ant.apache.org/ivy/history/2.2.0/install.html) and [ant](http://ant.apache.org/manual/install.html).

# Building from source

```BASH
ant         # build and run
ant test    # build and test
ant jar     # build and package jar
```

