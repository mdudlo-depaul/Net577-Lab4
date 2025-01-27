### Install Java Tools
```bash
sudo apt install openjdk-19-jre-headless
```

### Create Keycloak Certificate

1. Create a CSR for Keycloak
    - ```bash
      openssl req -new -newkey param:ecparam.pem -out keycloak.csr -keyout keycloak.pem -addext subjectAltName=DNS.1:keycloak.iamlab.depaulseclabs.com -nodes
        ```
2. Sign the certificate on a Windows PC
    - ```powershell
      certreq -submit -attrib -certificateTemplate:webserver
      ```

### Create DB Container
1. ```bash
   sudo docker run --name keycloak-postgres -e POSTGRES_PASSWORD=keycloak -p 5432:5432 -d postgres
   ```

### Create IDP Container
```bash
sudo docker run --name keycloak -p 8443:8443 \
-v /home/iamadmin/certs/:/certs \
-e KC_HOSTNAME=keycloak.iamlab.depaulseclabs.com \
-e KEYCLOAK_ADMIN=admin \
-e KEYCLOAK_ADMIN_PASSWORD=change_me \
quay.io/keycloak/keycloak:23.0.3 start \
--db=postgres --features=token-exchange \
--db-url="jdbc:postgresql://10.13.61.3/postgres" \
--db-username=postgres --db-password=keycloak \
--https-trust-store-file=/certs/truststore.jks \
--https-trust-store-password=keycloak \
--spi-truststore-file-file=/certs/truststore.jks \
--spi-truststore-file-password=keycloak \
--https-certificate-file=/certs/keycloak.cer \
--https-certificate-key-file=/certs/keycloak.pem
```