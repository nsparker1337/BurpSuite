
## Configuring burpsuite to intercepting android app and Bypass Instagram SSL pinning


#### Download Burp cert using curl

```bash
curl http://localhost:8080/cert -o cert.der
```

#### Change the certification format using openssl

```python
$ > openssl x509 -inform der -in cert.der -out burp.pem

$ > openssl x509 -inform pem -subject_hash_old -in burp.pem #This is to get hash of the file

$ > mv burp.pem <hash>.0 #rename the file again
```

#### Change the permission to install cert in the root system

```python
$ > adb shell
$ > su
$ > mount -o remount, rw /
$ > exit
```

#### Move the cert into device and install in system level

```python
$ > adb push <cert>.0 /system/etc/security/cacerts
```

#### Configure the proxy in Virtual device

```python
$ > adb shell settings put global http_proxy <ip>:<port>

$ > adb shell settings put global http_proxy :0 #This is for reset the proxy
```

#### Instagram SSL pinning bypass using frida

1. Download frida-server based on your architecture. To check use the following command
```python
$ > adb shell uname -a
```

2. Install frida tool using pip
```python
$ > pip3 install frida-tools
```

3. Move that frida file into system
```python
$ > unxz frida-server.xz
$ > adb root # might be required
$ > adb push frida-server /data/local/tmp/
$ > adb shell "chmod 755 /data/local/tmp/frida-server"
$ > adb shell "/data/local/tmp/frida-server &"
```

4. Download Insta frida injection script from github
```bash
https://github.com/Eltion/Instagram-SSL-Pinning-Bypass/blob/main/instagram-ssl-pinning-bypass.js
```

5. Finally Running the Command to hook the app
```python
$ > frida-ps -U -l ./instagram-ssl-pinning-bypass.js -f <package name> #com.instagram.android
```