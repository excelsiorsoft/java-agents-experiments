# SecurityFixer

Uses Byte Buddy to override the behavior of `System.setSecurityManager`.

NOTE: This is a demo, not a real security implementation.  For example, `System.getSecurityManager` isn't intercepted!  

## Compiling

```
mvn clean compile package
```

## Running

A little awkward.  I don't know how to get Maven exec to do anything useful.  Note that the interceptor (securityfixer-bootstrap) has to be on the bootstrap classpath, so
it must be packaged seperately from the agent:

```
java -javaagent:agent/target/securityfixer-agent-1.0-SNAPSHOT.jar=bootstrap/target/securityfixer-bootstrap-1.0-SNAPSHOT.jar securityfixer.Main
```

Running inside Eclipse via Maven:


![Main Tab](https://github.com/excelsiorsoft/java-agents-experiments/tree/master/images/Capture.jpg)

<p align="center">
  <img src="C:\Users\Simeon\Pictures\Capture.jpg" width="350"/>
 </p>
