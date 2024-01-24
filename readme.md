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
