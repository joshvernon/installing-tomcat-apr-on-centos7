## Installing tomcat APR native library - CentOS 7, Tomcat 7, & Java 8
* Install GNU Development tools  
```
yum group install "Development Tools"
```
* Install development header packages for APR and openssl  
```
yum install openssl-devel apr-devel
```
* Download the source code for the Apache Tomcat Native Library (because we installed Tomcat with yum, the source TAR is not present in `$CATALINA_HOME/bin`)  
```
cd /usr/share/tomcat/bin
wget http://apache.mirrors.lucidnetworks.net/tomcat/tomcat-connectors/native/1.2.16/source/tomcat-native-1.2.16-src.tar.gz
```
* Configure  
```
cd /usr/share/tomcat/bin/tomcat-native-1.2.16-src/native
./configure --with-apr=/usr/bin/apr-1-config --with-java-home=/usr/lib/jvm/java-1.8.0-openjdk --with-os-type=include/linux --with-ssl=yes --prefix=/usr/share/tomcat
```
NOTE: Make sure the JDK specified by `--with-java-home` is an actual JDK and not just a JRE!
* Fix the include directory path in the generated makefile  
    - In the `INCLUDES` directive change `-I/usr/lib/jvm/java-1.8.0-openjdk/include/include/linux` to `-I/usr/lib/jvm/java-1.8.0-openjdk/include/linux`    
* Compile the code  
```
make && make install
```
There should now be a `libtcnative` shared library in `/usr/share/tomcat/lib`
* Get tomcat to load the shared library. Create a file, `/usr/share/tomcat/conf/conf.d/loadapr.conf`, with the following lines:  
```
# Use JAVA_OPTS to set java.library.path for libtcnative.so  
JAVA_OPTS="-Djava.library.path=$CATALINA_HOME/lib"
```
* Start tomcat. The APR library should've been loaded!

Helpful links:  [Apache Tomcat Native Library documentation](https://tomcat.apache.org/native-doc/)

