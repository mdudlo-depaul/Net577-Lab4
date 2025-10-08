### ENV file
```bash
# Mysql Parameters
MYSQL_ROOT_PASSWORD=N3tS3c!
MYSQL_DATABASE=snipeit
MYSQL_USER=snipeit
MYSQL_PASSWORD=N3tS3c!

# Snipe-IT Settings
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:V0oc79NDOYzlM4eOuxmJonL0RVr3aYmgdz4acg7av1w=
APP_URL=https://snipe-it.iamlab.depaulseclabs.com:8090
APP_TIMEZONE=America/Chicago
APP_LOCALE=en

# Docker-specific variables
PHP_UPLOAD_LIMIT=100
```

### MYSQL Container
```bash
sudo docker run --name snipe-mysql --restart unless-stopped --env-file=my_env_file --mount source=snipesql-vol,target=/var/lib/mysql -d -P mysql:5.7
```

### SNIPE-IT Container
```bash
sudo docker run -d -p 8089:80 -p 8090:443 --name="snipeit" --restart unless-stopped --link snipe-mysql:mysql --mount source=snipe-vol,dst=/var/lib/snipeit --env-file=my_env_file snipe/snipe-it:v8.3.1
```

#### Copy Certs
```bash
sudo docker cp snipe-it.pem snipeit:/var/lib/snipeit/ssl/snipeit-ssl.key
```
```bash
sudo docker cp snipe-it.cer  snipeit:var/lib/snipeit/ssl/snipeit-ssl.crt
```
#### Restart container
```bash
sudo docker restart snipeit
```

