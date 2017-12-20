## Installing tomcat APR native library - CentOS 7, Tomcat 7, & Java 8
* Install GNU Development Tools  
```
# yum group install "Development Tools"
```
* Install development header packages for APR and openssl  
```
# yum install openssl-devel apr-devel
```
* Download and extract the source code for the Apache Tomcat Native Library (because we installed Tomcat with yum, the source tarball is not present in `$CATALINA_HOME/bin`)  
```
# cd /usr/share/tomcat/bin
# curl -O http://apache.mirrors.lucidnetworks.net/tomcat/tomcat-connectors/native/1.2.16/source/tomcat-native-1.2.16-src.tar.gz
# tar -xvzf tomcat-native-1.2.16-src.tar.gz
```
* Configure  
```
# cd /usr/share/tomcat/bin/tomcat-native-1.2.16-src/native
# ./configure --with-apr=/usr/bin/apr-1-config --with-java-home=/usr/lib/jvm/java-1.8.0-openjdk --with-os-type=include/linux --with-ssl=yes --prefix=/usr/share/tomcat
```
  NOTE: Make sure the JDK specified by `--with-java-home` is an actual JDK and not just a JRE!
* Fix the include directory path in the generated makefile  
    - In the `INCLUDES` directive change `-I/usr/lib/jvm/java-1.8.0-openjdk/include/include/linux` to `-I/usr/lib/jvm/java-1.8.0-openjdk/include/linux`    
* Compile the code  
```
# make && make install
```
  There should now be a `libtcnative` shared library in `/usr/share/tomcat/lib`
* Get tomcat to load the shared library. Create a file, `/usr/share/tomcat/conf/conf.d/loadapr.conf`, with the following lines:  
```
# Use JAVA_OPTS to set java.library.path for libtcnative.so  
JAVA_OPTS="-Djava.library.path=$CATALINA_HOME/lib"
```
  We need to use a separate config file in `conf/conf.d` because the root config file at `conf/tomcat.conf` doesn't support shell expansion.
* Start tomcat. The APR library should've been loaded!  
```
Dec 19, 2017 6:02:01 PM org.apache.catalina.core.AprLifecycleListener lifecycleEvent
INFO: Loaded APR based Apache Tomcat Native library 1.2.16 using APR version 1.4.8.
Dec 19, 2017 6:02:01 PM org.apache.catalina.core.AprLifecycleListener lifecycleEvent
INFO: APR capabilities: IPv6 [true], sendfile [true], accept filters [false], random [true].
Dec 19, 2017 6:02:01 PM org.apache.catalina.core.AprLifecycleListener initializeSSL
INFO: OpenSSL successfully initialized (OpenSSL 1.0.2k-fips  26 Jan 2017)
```

Helpful links:  [Apache Tomcat Native Library documentation](https://tomcat.apache.org/native-doc/)

