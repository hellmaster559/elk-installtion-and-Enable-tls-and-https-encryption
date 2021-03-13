# elk-installtion-and-Enable-the-tls-and-https-encryption
How you can install ELK and Enable the TLS Encryption and HTTPS Communication

1) Install Java and nginx

$ sudo apt install openjdk-11-jre apt-transport-https wget nginx

2) First, import Elastic's GPG key:

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

3) Next, use nano or your preferred text editor to create the following file:

$ sudo nano /etc/apt/sources.list.d/elastic.list

4) Inside that file, paste the following line, then exit and save the file:

deb https://artifacts.elastic.co/packages/6.x/apt stable main

5) Finally, you can update apt now that the repository is added:

$ sudo apt update

6) Enter the following command in your terminal to install Elasticsearch and Kibana:

$ sudo apt install elasticsearch kibana

7) Next, you need to edit the Kibana configuration file to set the host server as localhost:

$ sudo nano /etc/kibana/kibana.yml

8) Inside kibana.yml, find the following line and uncomment it:

server.host: "localhost"

9) Save your changes to the configuration file and exit it. Then, restart Kibana and start up Elasticsearch:

$ sudo systemctl restart kibana
$ sudo systemctl start elasticsearch

10) we need to create a new Nginx configuration file to serve our instance of Kibana:

$ sudo nano /etc/nginx/sites-available/kibana

11) Inside this new file, you can paste the following code:

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

12) Once the new configuration is saved, you need to remove the existing default config, and create a new symlink in sites-enabled for Kibana.

$ sudo rm /etc/nginx/sites-enabled/default
$ sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana

13) Lastly, restart Nginx for all of the changes to take effect:

$ sudo systemctl restart nginx

Enable the TLS Encryption and HTTPS Communication

1)	Stop Elasticsearch and Kibana service:
a.	Service elasticsearch stop
b.	Service kibana stop
2)	Generate Certificates for Elasticsearch:
a.	/usr/share/elasticsearch/bin/elasticsearch-certutil  ca
b.	/usr/share/elasticsearch/bin/elasticsearch-certutil  cert –ca elastic-stack-ca.p12
3)	Copy the file to Elasticsearch folder and change ownership and permissions:
a.	cp /usr/share/elasticsearch/elastic-certificates.p12 /etc/elasticsearch/
b.	chown root.elasticsearch /etc/elasticsearch/elastic-certificates.p12
c.	chmod 660 /etc/elasticsearch/elastic-certificates.p12
4)	Generate Certificate For Elasticsearch and Kibana
a.	/usr/share/elasticsearch/bin/elasticsearch-certutil  http
i.	For Elasticserach
1.	cd /usr/share/elasticsearch
2.	unzip elasticsearch-ssl-http.zip
3.	cp  /usr/share/elasticsearch/elasticsearch/http.p12 /etc/elasticsearch/
4.	chown root.elasticsearch /etc/elasticsearch/http.p12
5.	chmod 660 /etc/elasticsearch/http.p12


ii.	For Kibana
1.	cp  /usr/share/elasticsearch/kibana/elasticsearch-ca.pem /etc/kibana/
5)	Edit elasticserach.yml
a.	vi /etc/elasticsearch/elasticsearch.yml
6)	Add the Following Lines at the end in elasticsearch.yml
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: "http.p12"
7)	Start the Elasticserach service:
a.	systemctl start elasticsearch
8)	Edit Kibana.yml
a.	vi /etc/kibana/kibana.yml
9)	Edit Following line in kibana.yml
elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.ssl.certificateAuthorities: [/path/to/CA.pem]
TO
elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.ssl.certificateAuthorities: [/etc/kibana/elasticserach-ca.pem]
elasticserach.ssl.verificationMode: none

10)Start the Kibana Service:
		a. service kibana start
1) Install Java and nginx

$ sudo apt install openjdk-11-jre apt-transport-https wget nginx

2) First, import Elastic's GPG key:

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

3) Next, use nano or your preferred text editor to create the following file:

$ sudo nano /etc/apt/sources.list.d/elastic.list

4) Inside that file, paste the following line, then exit and save the file:

deb https://artifacts.elastic.co/packages/6.x/apt stable main

5) Finally, you can update apt now that the repository is added:

$ sudo apt update

6) Enter the following command in your terminal to install Elasticsearch and Kibana:

$ sudo apt install elasticsearch kibana

7) Next, you need to edit the Kibana configuration file to set the host server as localhost:

$ sudo nano /etc/kibana/kibana.yml

8) Inside kibana.yml, find the following line and uncomment it:

server.host: "localhost"

9) Save your changes to the configuration file and exit it. Then, restart Kibana and start up Elasticsearch:

$ sudo systemctl restart kibana
$ sudo systemctl start elasticsearch

10) we need to create a new Nginx configuration file to serve our instance of Kibana:

$ sudo nano /etc/nginx/sites-available/kibana

11) Inside this new file, you can paste the following code:

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

12) Once the new configuration is saved, you need to remove the existing default config, and create a new symlink in sites-enabled for Kibana.

$ sudo rm /etc/nginx/sites-enabled/default
$ sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana

13) Lastly, restart Nginx for all of the changes to take effect:

$ sudo systemctl restart nginx

Enable the TLS Encryption and HTTPS Communication

1)	Stop Elasticsearch and Kibana service:
a.	Service elasticsearch stop
b.	Service kibana stop
2)	Generate Certificates for Elasticsearch:
a.	/usr/share/elasticsearch/bin/elasticsearch-certutil  ca
b.	/usr/share/elasticsearch/bin/elasticsearch-certutil  cert –ca elastic-stack-ca.p12
3)	Copy the file to Elasticsearch folder and change ownership and permissions:
a.	cp /usr/share/elasticsearch/elastic-certificates.p12 /etc/elasticsearch/
b.	chown root.elasticsearch /etc/elasticsearch/elastic-certificates.p12
c.	chmod 660 /etc/elasticsearch/elastic-certificates.p12
4)	Generate Certificate For Elasticsearch and Kibana
a.	/usr/share/elasticsearch/bin/elasticsearch-certutil  http
i.	For Elasticserach
1.	cd /usr/share/elasticsearch
2.	unzip elasticsearch-ssl-http.zip
3.	cp  /usr/share/elasticsearch/elasticsearch/http.p12 /etc/elasticsearch/
4.	chown root.elasticsearch /etc/elasticsearch/http.p12
5.	chmod 660 /etc/elasticsearch/http.p12


ii.	For Kibana
1.	cp  /usr/share/elasticsearch/kibana/elasticsearch-ca.pem /etc/kibana/
5)	Edit elasticserach.yml
a.	vi /etc/elasticsearch/elasticsearch.yml
6)	Add the Following Lines at the end in elasticsearch.yml
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: "http.p12"
7)	Start the Elasticserach service:
a.	systemctl start elasticsearch
8)	Edit Kibana.yml
a.	vi /etc/kibana/kibana.yml
9)	Edit Following line in kibana.yml
elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.ssl.certificateAuthorities: [/path/to/CA.pem]
TO
elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.ssl.certificateAuthorities: [/etc/kibana/elasticserach-ca.pem]
elasticserach.ssl.verificationMode: none

10)Start the Kibana Service:
		a. service kibana start
