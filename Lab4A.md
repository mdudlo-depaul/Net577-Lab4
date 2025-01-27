### Install Java Tools
```bash
sudo apt install openjdk-21-jre-headless
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
### Create Java Keystore
```bash
keytool -importcert -file <yourname>-ca.cer -keystore truststore.jks
```
copy truststore.jks into /home/iamadmin/certs

### Create DB Container
1. ```bash
   sudo docker run --name keycloak-postgres -e POSTGRES_PASSWORD=keycloak -p 5432:5432 -d postgres
   ```

### Create IDP Container
1. Create container, monitor the terminal output for errors
    - ```bash
      sudo docker run --name keycloak -p 8443:8443 \
      -v /home/iamadmin/certs/:/certs \
      -e KC_HOSTNAME=keycloak.iamlab.depaulseclabs.com \
      -e KEYCLOAK_ADMIN=admin \
      -e KEYCLOAK_ADMIN_PASSWORD=change_me \
      quay.io/keycloak/keycloak:26.1.0 start \
      --db=postgres --features=token-exchange \
      --db-url="jdbc:postgresql://10.77.10.3/postgres" \
      --db-username=postgres --db-password=keycloak \
      --https-trust-store-file=/certs/truststore.jks \
      --https-trust-store-password=keycloak \
      --spi-truststore-file-file=/certs/truststore.jks \
      --spi-truststore-file-password=keycloak \
      --https-certificate-file=/certs/keycloak.cer \
      --https-certificate-key-file=/certs/keycloak.pem
      ```
2. Stop the container and restart it in non-interactive mode
    - Enter CRTL+C on the terminal
    - ```
      sudo docker start keycloak
      ```
