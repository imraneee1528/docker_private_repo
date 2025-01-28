# Installations of Private Repositories
<pre>
  apt install curl apt-transport-https ca-certificates  -y
  

</pre>
<pre>
  apt install docker.io docker-compose -y
  apt install nginx apache2-utils -y
</pre>
<pre>
  mkdir ~/private-registry
  mkdir ~/private-registry/registry-data
  cd ~/private-registry
  vim /etc/nginx/conf.d/registry.conf
  vim docker-compose.yml
  vim /etc/hosts  
</pre>

<pre>
  mkdir /etc/nginx/ssl
  cd /etc/nginx/ssl
   openssl genrsa -out private.imran.com.key 2048
</pre>
vim csr.cnf
<pre>
[req]
default_bits       = 2048
prompt             = no
default_md         = sha256
distinguished_name = dn
req_extensions     = req_ext

[dn]
C = US
ST = State
L = City
O = Organization
OU = Unit
CN = private.imran.com

[req_ext]
subjectAltName = @alt_names

[alt_names]
DNS.1 = private.imran.com
</pre>
<pre>
openssl req -new -key private.imran.com.key -out private.imran.com.csr -config csr.cnf
</pre>
vim  cert.cnf
<pre>
[v3_ca]
subjectAltName = @alt_names

[alt_names]
DNS.1 = private.imran.com
</pre>

<pre>
openssl x509 -req -in private.imran.com.csr -signkey private.imran.com.key -out private.imran.com.crt -days 365 -extfile cert.cnf -extensions v3_ca


sudo mkdir -p /etc/docker/certs.d/private.imran.com
sudo cp private.imran.com.crt /etc/docker/certs.d/private.imran.com/ca.crt
</pre>
vim /etc/nginx/nginx.conf
<pre>
client_max_body_size 4000m;
server_names_hash_bucket_size 64;
</pre>
<pre>
systemctl restart nginx
mkdir ~/private-registry/auth
cd ~/private-registry/auth
htpasswd -Bc registry.password adminuser
cd ~/private-registry
docker compose up -d
</pre>
<pre>
docker login https://private.imran.com
docker tag ubuntu22-image private.linuxbuz.com/ubuntu22-image
docker push private.imran.com/ubuntu22-image
</pre>









