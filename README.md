# POC Distributed lock


This is a POC to have distributed lock system.

1. The client (a javascript application running in a browser) open a
   session, all the locks will be associated with this session.
  
2. The client ask for a lock, if available the server return a LOCKED
   message. If the lock is already taken the server return a WAIT
   message. The call is not blocking.
   
3. When the lock is released the server send a RETRY message with the
   lockname to all the clients that were waiting for it. They can 
   ask again for the lock.

4. The client can close the session, releasing all its locks.


There is also a `mlock` and `munlock` operations to ask for multiple
locks in an atomic way.

The system use websocket between browser and the server, the
bi-directional channel stay open during the session.

The system support multiple servers.

The websocket is hanlded by
[Atmosphere](https://github.com/Atmosphere/atmosphere/wiki) and
persistence and communication between servers using
[Redis](http://redis.io).

## Lock sequence

![The lock sequence](https://raw.github.com/bdelbosc/distlock/master/lock-sequence.png)

## Websocket protocol
Request and response are JSON encoded:

- Request:

        {'action': String, 'params': String[], 'time': stamp}


- Response: 

        {'status': String, 'message': String, 'time': stamp}

Implemented action:

- connect sessionName
- close
- lock [lockname]
- unlock lockname
- mlock [lockname...]
- munlock [lockname...]
   
Status:

- LOCKED
- FAIL
- WAIT
- RETRY lockname


## Installation

1. Get the [latest tomcat 7](http://tomcat.apache.org/download-70.cgi).
2. Build:

         mvn clean package
     
3. Deploy:

         cp ./target/distlock.war $TOMCAT/webapps/ && $TOMCAT/bin/startup.sh

4. Test using [http://localhost:8080/distlock/](http://localhost:8080/distlock/)

      

## Load testing

This requires to install:

- [funkload](http://funkload.nuxeo.com/install.html) 
- [Websocket for python](https://github.com/Lawouach/WebSocket-for-Python.git).

Test with 10 clients:

        cd ftest/funkload && fl-run-bench test_Distlock.py Distlock.test_distlock -c 10


## References

- [Creating lock with Redis](http://dr-josiah.blogspot.fr/2012/01/creating-lock-with-redis.html)
- [Jedis Advanced Usage](https://github.com/xetorthio/jedis/wiki/AdvancedUsage)
- [Atmosphere Wiki](https://github.com/Atmosphere/atmosphere/wiki)



## About Nuxeo

Nuxeo provides a modular, extensible Java-based [open source software platform for enterprise content management](http://www.nuxeo.com/en/products/ep) and packaged applications for [document management](http://www.nuxeo.com/en/products/document-management), [digital asset management](http://www.nuxeo.com/en/products/dam) and [case management](http://www.nuxeo.com/en/products/case-management). Designed by developers for developers, the Nuxeo platform offers a modern architecture, a powerful plug-in model and extensive packaging capabilities for building content applications.

More information on: <http://www.nuxeo.com/>
