# SecurityFixer

Uses Byte Buddy to override the behavior of `System.setSecurityManager`.

NOTE: This is a demo, not a real security implementation.  For example, `System.getSecurityManager` isn't intercepted!  

## Compiling

```
mvn clean compile package
```

or one could also compile inside Eclipse via Maven, in the parent ``securityfixer`` project:


![Main Tab](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture.JPG)

To prevent the following Maven error

![Maven Eclipse Error](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture3.JPG)

 make sure to have the Java VM argument properly set up in Eclipse:


![JRE Tab](https://github.com/excelsiorsoft/java-agents-experiments/blob/master/images/Capture2.JPG)

## Running

Note that the interceptor (securityfixer-bootstrap) has to be on agent's bootstrap classpath, so
it must be packaged seperately from the agent and passed in via its javaagent argument.

`byte-buddy-1.0.0.jar` must be inside `java-agents-experiments\securityfixer\agent\target` along with the genarated `securityfixer-agent-1.0-SNAPSHOT.jar` as the latter depends on the former.  This is achieved by including the following plugin, which performs the copying, in `securityfixer-agent/pom.xml`: 

    
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


So that unnecessary dependencies (such as `securityfixer-bootstrap`) are not being copied by the above plugin along with the `byte-buddy-1.0.0.jar` I needed to change their scope to `provided`.  `maven-dependency-plugin` seems to skip copying dependencies with that scope to its destination folder.

To be able to run it as executable jar we need to add the `<mainClass>` stanza to `securityfixer-example/pom.xml`:

    		<plugin>
				<artifactId>maven-jar-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<archive>
						<manifest>
							<mainClass>securityfixer.Main</mainClass>
						</manifest>
					</archive>
				</configuration>
			</plugin>

.


    $ java -javaagent:agent/target/securityfixer-agent-1.0-SNAPSHOT.jar=bootstrap/target/securityfixer-bootstrap-1.0-SNAPSHOT.jar -jar example/target/securit yfixer-example-1.0-SNAPSHOT.jar


    Security manager is set!    
    ATTACK FAILED: SecurityManager cannot be reset!






