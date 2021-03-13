# elk-installtion-and-Enable-the-tls-and-https-encryption
How you can install ELK and Enable the TLS Encryption and HTTPS Communication

1) Install Java and nginx
```
$ sudo apt install openjdk-11-jre apt-transport-https wget nginx
```
2) First, import Elastic's GPG key:
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
3) Next, use nano or your preferred text editor to create the following file:
```
$ sudo nano /etc/apt/sources.list.d/elastic.list
```
4) Inside that file, paste the following line, then exit and save the file:
```
deb https://artifacts.elastic.co/packages/6.x/apt stable main
```
5) Finally, you can update apt now that the repository is added:
```
$ sudo apt update
```
6) Enter the following command in your terminal to install Elasticsearch and Kibana:
```
$ sudo apt install elasticsearch kibana
```
7) Next, you need to edit the Kibana configuration file to set the host server as localhost:
```
$ sudo nano /etc/kibana/kibana.yml
```
8) Inside kibana.yml, find the following line and uncomment it:
```
server.host: "localhost"
```
9) Save your changes to the configuration file and exit it. Then, restart Kibana and start up Elasticsearch:
```
$ sudo systemctl restart kibana
$ sudo systemctl start elasticsearch
```
10) Create the Certificate Configuration File
```
$ sudo nano localhost.conf
```

``` 
[req]
default_bits       = 2048
default_keyfile    = localhost.key
distinguished_name = req_distinguished_name
req_extensions     = req_ext
x509_extensions    = v3_ca

[req_distinguished_name]
countryName                 = Country Name (2 letter code)
countryName_default         = US
stateOrProvinceName         = State or Province Name (full name)
stateOrProvinceName_default = New York
localityName                = Locality Name (eg, city)
localityName_default        = Rochester
organizationName            = Organization Name (eg, company)
organizationName_default    = localhost
organizationalUnitName      = organizationalunit
organizationalUnitName_default = Development
commonName                  = Common Name (e.g. server FQDN or YOUR name)
commonName_default          = localhost
commonName_max              = 64

[req_ext]
subjectAltName = @alt_names

[v3_ca]
subjectAltName = @alt_names

[alt_names]
DNS.1   = localhost
DNS.2   = 127.0.0.1
```

12) Create the Certificate using OpenSSL
```
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.crt -config localhost.conf
```
13) Copy the public key to the /etc/ssl/certs directory
```
$ sudo cp localhost.crt /etc/ssl/certs/localhost.crt
```
14) Copy the private key to the /etc/ssl/private directory
```
$ sudo cp localhost.key /etc/ssl/private/localhost.key
```
15) Update the Nginx Configuration File to Load the Certificate Key Pair
```
$ sudo nano /etc/nginx/sites-available/default
```
```
server {
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        server_name localhost;

        ssl_certificate /etc/ssl/certs/localhost.crt;
        ssl_certificate_key /etc/ssl/private/localhost.key;

        ssl_protocols TLSv1.2 TLSv1.1 TLSv1;

        location / {
           proxy_pass http://localhost:5601;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
```
16) Once the new configuration is saved, you need to remove the existing default config, and create a new symlink in sites-enabled for Kibana.
```
$ sudo rm /etc/nginx/sites-enabled/default
$ sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana
```
17) Lastly, restart Nginx for all of the changes to take effect:
```
$ sudo systemctl restart nginx
```

