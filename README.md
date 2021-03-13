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
10) we need to create a new Nginx configuration file to serve our instance of Kibana:
```
$ sudo nano /etc/nginx/sites-available/kibana
```
11) Inside this new file, you can paste the following code:
```
server {
        listen 80;

        server_name your-site.com;

        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/htpasswd.kibana;

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
12) Once the new configuration is saved, you need to remove the existing default config, and create a new symlink in sites-enabled for Kibana.
```
$ sudo rm /etc/nginx/sites-enabled/default
$ sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana
```
13) Lastly, restart Nginx for all of the changes to take effect:
```
$ sudo systemctl restart nginx
```

