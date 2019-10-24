# fabric8-maven-plugin examples [![Build Status](https://travis-ci.org/daggerok/fabric8-maven-plugin-examples.svg?branch=master)](https://travis-ci.org/daggerok/fabric8-maven-plugin-examples)

```bash
 while true ; do docker ps ; sleep 0.5s ; clear ; done
```

## step0

```xml
<plugin>
    <groupId>io.fabric8</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <configuration>
        <autoCreateCustomNetworks>true</autoCreateCustomNetworks>
        <images>
            <image>
                <name>daggerok/${project.parent.artifactId}-${project.artifactId}:${project.version}</name>
                <run>
                    <imagePullPolicy>Never</imagePullPolicy>
                    <ports>
                        <port>8080:8080</port>
                    </ports>
                    <wait>
                        <http>
                            <url>http://127.0.0.1:8080</url>
                            <method>GET</method>
                            <status>200..399</status>
                        </http>
                        <healthy>true</healthy>
                        <time>10000</time>
                        <shutdown>500</shutdown>
                    </wait>
                </run>
            </image>
        </images>
    </configuration>
</plugin>
```

* `docker build` using Dockerfile
* `docker run` using XML DSL

```bash
./mvnw -f ./step0-zero-configuration clean package docker:build docker:start
http :8080
./mvnw -f ./step0-zero-configuration docker:stop docker:remove
```

## step1

```xml
<plugin>
    <groupId>io.fabric8</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <configuration>
        <verbose>true</verbose>
        <imagePullPolicy>IfNotPresent</imagePullPolicy>
        <autoCreateCustomNetworks>true</autoCreateCustomNetworks>
        <images>
            <image>
                <name>daggerok/${project.parent.artifactId}-${project.artifactId}:${project.version}</name>
                <build>
                    <from>daggerok/jboss-eap-7.2:7.2.0-alpine</from>
                    <ports>
                        <port>8080</port>
                    </ports>
                    <assembly>
                        <mode>dir</mode>
                        <inline>
                            <id>no-Dockerfile</id>
                            <files>
                                <file>
                                    <source>${project.build.directory}/${project.artifactId}-${project.version}.war</source>
                                    <destName>ROOT.war</destName>
                                </file>
                            </files>
                        </inline>
                        <name>.</name>
                        <targetDir>${JBOSS_HOME}/standalone/deployments</targetDir>
                    </assembly>
                    <healthCheck>
                        <interval>3s</interval>
                        <timeout>2s</timeout>
                        <retries>9</retries>
                        <cmd>wget -q --spider http://127.0.0.1:8080/ || exit 1</cmd>
                    </healthCheck>
                    <entryPoint>
                        <shell>${JBOSS_HOME}/bin/standalone.sh -b 0.0.0.0</shell>
                    </entryPoint>
                </build>
                <run>
                    <ports>
                        <port>8080:8080</port>
                    </ports>
                    <wait>
                        <http>
                            <url>http://127.0.0.1:8080</url>
                            <method>GET</method>
                            <status>200..399</status>
                        </http>
                        <healthy>true</healthy>
                        <time>10000</time>
                        <shutdown>500</shutdown>
                    </wait>
                </run>
            </image>
        </images>
    </configuration>
    <executions>
        <execution>
            <id>docker-build</id>
            <goals><goal>build</goal></goals>
        </execution>
    </executions>
</plugin>
```

* `docker build` using verbose XML DSL
* `docker run` using XML DSL

```bash
./mvnw -f ./step1-zero-configuration clean package docker:build docker:start
http :8080
./mvnw -f ./step1-zero-configuration docker:stop docker:remove
```

## step2

```xml
<plugin>
    <groupId>io.fabric8</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <configuration>
        <verbose>true</verbose>
        <imagePullPolicy>IfNotPresent</imagePullPolicy>
        <autoCreateCustomNetworks>true</autoCreateCustomNetworks>
        <images>
            <image>
                <name>daggerok/${project.parent.artifactId}-${project.artifactId}:${project.version}</name>
                <build>
                    <from>daggerok/jboss-eap-7.2:7.2.0-alpine</from>
                    <ports>
                        <port>8080</port>
                    </ports>
                    <assembly>
                        <descriptorRef>rootWar</descriptorRef>
                        <name>.</name>
                        <targetDir>${JBOSS_HOME}/standalone/deployments</targetDir>
                    </assembly>
                    <healthCheck>
                        <interval>3s</interval>
                        <timeout>2s</timeout>
                        <retries>9</retries>
                        <cmd>wget -q --spider http://127.0.0.1:8080/ || exit 1</cmd>
                    </healthCheck>
                    <entryPoint>
                        <shell>${JBOSS_HOME}/bin/standalone.sh -b 0.0.0.0</shell>
                    </entryPoint>
                </build>
                <run>
                    <ports>
                        <port>8080:8080</port>
                    </ports>
                    <wait>
                        <http>
                            <url>http://127.0.0.1:8080</url>
                            <method>GET</method>
                            <status>200..399</status>
                        </http>
                        <healthy>true</healthy>
                        <time>10000</time>
                        <shutdown>500</shutdown>
                    </wait>
                </run>
            </image>
        </images>
    </configuration>
    <executions>
        <execution>
            <id>docker-build</id>
            <goals><goal>build</goal></goals>
        </execution>
    </executions>
</plugin>
```

* `docker build` using nice and short XML DSL with `rootWar` descriptorRef
* `docker run` using XML DSL

```bash
./mvnw -f ./step2-zero-configuration clean package docker:build docker:start
http :8080
./mvnw -f ./step2-zero-configuration docker:stop docker:remove
```

## links

* https://maven.fabric8.io/
* https://github.com/fabric8io/docker-maven-plugin
* https://github.com/fabric8io/docker-maven-plugin/tree/master/samples
* https://github.com/fabric8io/fabric8-maven-plugin
* https://github.com/fabric8io/fabric8-maven-plugin/tree/master/samples
* https://maven.apache.org/plugins/maven-assembly-plugin/assembly.html
