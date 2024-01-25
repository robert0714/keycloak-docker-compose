# Keycloak use Nginx to protect 

## Reference
* https://docs.nginx.com/nginx-management-suite/admin-guides/access-control/oidc-keycloak/
* https://www.nginx.com/blog/harnessing-power-convenience-of-javascript-for-each-request-with-nginx-javascript-module/
* https://www.nginx.com/blog/harnessing-power-convenience-of-javascript-for-each-request-with-nginx-javascript-module/#njs-enable
* https://github.com/keycloak/keycloak-community
* https://github.com/keycloak/keycloak-containers
* https://github.com/keycloak/keycloak/blob/main/examples/providers/authenticator/README.md
## Conclusion
**The NGINX JavaScript module is included by default in the official NGINX Docker image**
## Special Case
* When it was used in GCP VM, **HTTPS Required** would be happend.
* Using cli to disable **HTTPS Required**  (refer: https://stackoverflow.com/questions/30622599/https-required-while-logging-in-to-keycloak-as-admin)
     ```bash
      docker exec -it keycloak bash

     /opt/keycloak/bin/kcadm.sh update realms/master -s sslRequired=NONE --server http://localhost:8083/auth  --realm master --user admin
     ```

## Test
http://localhost/auth/


# Keycloak Variable
* https://www.keycloak.org/server/all-config#category-http
* https://www.keycloak.org/server/containers


```bash
$ docker pull keycloak/keycloak:17.0.0
$ docker run -p 8080:8080 \
    -e KEYCLOAK_ADMIN=admin \
    -e KC_HOSTNAME=localhost \
    -e KEYCLOAK_ADMIN_PASSWORD=password \
    keycloak/keycloak:17.0.0  start-dev
```    
# Keycloak Theme
## Change Theme
* Configure >> Realm Settings >> Theme
* Manage >> Clients >> Clients List >> ${{ CUSTOM_CLIENT_ID }} >> Settings >> Login settings >> Login theme

## Related reference
* [Customising Keycloak Theme](https://youtu.be/NH8dP647Lc0?list=PLeGNmkzI56BTjRxNGxUhh4k30FD_gy0pC&t=1200)
* https://www.baeldung.com/keycloak-custom-login-page
 * [How to Create a Custom Login Theme for Keycloak in Docker](https://czetsuya.medium.com/how-to-create-a-custom-login-theme-for-keycloak-9b9f5f271fc0)
       * [sourceCode](https://github.com/czetsuya/ct-keycloak-iam)
       * [Alfresco Keycloak Theme](https://github.com/Alfresco/alfresco-keycloak-theme)
# Create Keycloak Instance by Azure WebApp
* Referense: https://www.youtube.com/watch?v=neHFkd8c-gc&t=202s
  * 20-  version for Azure
  ```yaml
  version: 3
  services:
    keycloak:
       image: jboss/keycloak:16.1.1
       container_name: keycloak
       restart: always
       environment:
         - KEYCLOAK_USER=admin
         - KEYCLOAK_PASSWORD=Pa55w0rd
         - PROXY_ADDRESS_FORWARDING=true
         - KC_HOSTNAME_PATH=/auth
         - KC_HOSTNAME_STRICT=false
         - KC_HOSTNAME_STRICT_HTTPS=false
         - KC_HTTP_ENABLED=true
         - KC_HTTP_RELATIVE_PATH=auth
         - TZ=Asia/Taipei
       volumes:
         - ${WEBAPP_STORAGE_HOME}/data:/opt/jboss/keycloak/standalone/data
  ```
  * 20+  version for Azure
  ```yaml
  version: 3
  services:
    keycloak:
       image: quay.io/keycloak/keycloak:23.0
       restart: always
       command: start
       environment:
         - KEYCLOAK_ADMIN=admin
         - KEYCLOAK_ADMIN_PASSWORD=Pa55w0rd
         - PROXY_ADDRESS_FORWARDING=true
         - KC_HOSTNAME_PATH=/auth
         - KC_HOSTNAME_STRICT=false
         - KC_PROXY=edge
         - KC_HTTP_RELATIVE_PATH=auth
         - TZ=Asia/Taipei
  ```
# If you have custom user provider
- [Using Custom User Providers with Keycloak](https://www.baeldung.com/java-keycloak-custom-user-providers)
- [Custom Protocol Mapper with Keycloak](https://www.baeldung.com/keycloak-custom-protocol-mapper)
- docker-compose.yml:
```yml
 volumes:
   - $PWD/keycloak-custom-providers-brycpt-with-dependencies.jar:/opt/keycloak/providers/custom-providers.jar
```
* pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.baeldung</groupId>
    <artifactId>keycloak-custom-providers</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.7</version>
        <relativePath />
    </parent>

    <dependencies>
    
        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-services</artifactId>
            <version>${keycloak.version}</version>
            <exclusions>
            	<exclusion>
            		<groupId>org.jboss</groupId>
            		<artifactId>jandex</artifactId>
            	</exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>
        <dependency>
		    <groupId>at.favre.lib</groupId>
		    <artifactId>bcrypt</artifactId>
		    <version>0.10.2</version>
		</dependency>
        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-model-legacy</artifactId>
            <version>${keycloak.version}</version>
            <scope>provided</scope>
        </dependency>
        
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Embedded Keycloak sample -->
         

        <!-- test -->

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
		    <groupId>org.springframework.security</groupId>
		    <artifactId>spring-security-crypto</artifactId> 
		    <scope>test</scope>
		</dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>**/*IntegrationTest.java</exclude>
                    </excludes>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <testExcludes>
                        <testExclude>**/*IntegrationTest.java</testExclude>
                    </testExcludes>
                </configuration>
            </plugin>
            <plugin>
            	<groupId>org.apache.maven.plugins</groupId>
	            <artifactId>maven-assembly-plugin</artifactId> 
	            <configuration>
	                <descriptorRefs>
	                    <descriptorRef>jar-with-dependencies</descriptorRef>
	                </descriptorRefs>
	            </configuration> 
	            <executions>
	                <execution>
	                    <id>make-assembly</id>
	                    <phase>package</phase>
	                    <goals>
	                        <goal>single</goal>
	                    </goals>
	                </execution>
	            </executions> 
	        </plugin>						
        </plugins>
    </build>

    <properties>
        <!-- non-dependencies -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>17</java.version>
        <keycloak.version>22.0.5</keycloak.version>
        <!-- these should be updated together with Keycloak -->
        <!-- check keycloak-dependencies-server-all effective pom -->
        <infinispan.version>14.0.6.Final</infinispan.version>
    </properties>
</project>
```
