This a repo for Almalinux8
1. Install missing build dependencies
Run this on your AlmaLinux 9.8 host before compiling:

bash
sudo dnf install redhat-rpm-config gcc make wget tar \
    apr-devel apr-util-devel pcre-devel \
    openssl-devel expat-devel zlib-devel \
    libnghttp2-devel jansson-devel libcurl-devel -y
redhat-rpm-config → provides /usr/lib/rpm/redhat/redhat-hardened-ld

zlib-devel → needed for mod_deflate (compression)

libnghttp2-devel → needed for HTTP/2

jansson-devel → needed for mod_md (certificate management)

libcurl-devel → needed for mod_md and other modules

2: Enable HTTP/2 and mod_md
If you want those features, you’ll need extra repos:

bash
sudo dnf install epel-release -y
sudo dnf install libnghttp2 libnghttp2-devel jansson jansson-devel -y
Sometimes jansson-devel is only available via PowerTools/CRB repo:

bash
sudo dnf config-manager --set-enabled crb
sudo dnf install jansson-devel -y

###############################################3

Generate SSL Files
1. Create a Private Key
bash
openssl genrsa -out server.key 2048
This produces your private key (server.key). Keep it secure (0600 permissions).

2. Create a Certificate Signing Request (CSR)
bash
openssl req -new -key server.key -out server.csr \
    -subj "/C=PH/ST=Calabarzon/L=Binangonan/O=MyCompany/OU=IT/CN=example.com"
Adjust CN (Common Name) to match your domain (or localhost for dev).

Other fields can be customized or left generic.

3. Create a Self‑Signed Root CA
bash
openssl genrsa -out rootCA.key 4096
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 \
    -out rootCA.crt -subj "/C=PH/ST=Calabarzon/O=MyRootCA/CN=MyRootCA"
This is your Root CA (rootCA.crt + rootCA.key).

4. Sign the Server Certificate with Root CA
bash
openssl x509 -req -in server.csr -CA rootCA.crt -CAkey rootCA.key \
    -CAcreateserial -out server.crt -days 365 -sha256 \
    -extfile <(printf "basicConstraints=CA:FALSE\nsubjectAltName=DNS:example.com")
This produces a proper server certificate (server.crt) with CA:FALSE.

5. Create the Chain File
bash
cat server.crt rootCA.crt > server-chain.crt
This concatenates the server certificate and the Root CA into a chain file (server-chain.crt).
