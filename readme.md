# Keycloak use Nginx to protect 

## Reference
* https://docs.nginx.com/nginx-management-suite/admin-guides/access-control/oidc-keycloak/
* https://www.nginx.com/blog/harnessing-power-convenience-of-javascript-for-each-request-with-nginx-javascript-module/
* https://www.nginx.com/blog/harnessing-power-convenience-of-javascript-for-each-request-with-nginx-javascript-module/#njs-enable
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