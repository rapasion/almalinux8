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
