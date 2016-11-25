# SecurityFixer

Uses Byte Buddy to override the behavior of `System.setSecurityManager`.

NOTE: This is a demo, not a real security implementation.  For example, `System.getSecurityManager` isn't intercepted!  

## Compiling

```
mvn clean compile package
```

Compiling inside Eclipse via Maven, in the parent ``securityfixer`` project:


![Main Tab](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture.JPG)

To prevent the following Maven error

![Maven Eclipse Error](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture3.JPG)

 make sure to have the Java VM argument properly set up in Eclipse:


![JRE Tab](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture2.JPG)

## Running

Note that the interceptor (securityfixer-bootstrap) has to be on agent's bootstrap classpath, so
it must be packaged seperately from the agent and passed in via its javaagent argument.

`byte-buddy-1.0.0.jar` must be inside `java-agents-experiments\securityfixer\agent\target` along with the genarated `securityfixer-agent-1.0-SNAPSHOT.jar` as the latter depends on the former.  This is achieved by including the following plugin in `securityfixer-agent/pom.xml`: 

    
    			<plugin>
    				<groupId>org.apache.maven.plugins</groupId>
    				<artifactId>maven-dependency-plugin</artifactId>
    				<version>2.8</version>
    				<executions>
    					<execution>
    						<id>copy-dependencies</id>
    						<phase>prepare-package</phase>
    						<goals>
    							<goal>copy-dependencies</goal>
    						</goals>
    						<configuration>
    							<outputDirectory>${project.build.directory}</outputDirectory>
    							<includeScope>runtime</includeScope>
    						</configuration>
    					</execution>
    				</executions>
    			</plugin>
    
as well as the following reference in the `<Boot-Class-Path>` to the artifact produced by above stanza:

			<plugin>
				<artifactId>maven-jar-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<archive>
						<manifestEntries>
							<Can-Redefine-Classes>true</Can-Redefine-Classes>
							<Can-Retransform-Classes>true</Can-Retransform-Classes>
							<Agent-Class>com.excelsiorsoft.securityfixer.agent.SecurityFixerAgent</Agent-Class>
							<Premain-Class>com.excelsiorsoft.securityfixer.agent.SecurityFixerAgent</Premain-Class>
							<Boot-Class-Path>byte-buddy-1.0.0.jar</Boot-Class-Path>
						</manifestEntries>
					</archive>
				</configuration>
			</plugin>


```
java $ java -javaagent:agent/target/securityfixer-agent-1.0-SNAPSHOT.jar=bootstrap/target/securityfixer-bootstrap-1.0-SNAPSHOT.jar -jar example/target/securit yfixer-example-1.0-SNAPSHOT.jar
```

    Security manager is set!    
    ATTACK FAILED: SecurityManager cannot be reset!


