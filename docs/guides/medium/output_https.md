---
layout: default
title: Output in HTTPs
parent: Medium
grand_parent: Guides
---

# Output in HTTPs

There are several HTTP-based nAttrMon outputs. Following the alphanumeric loading order on the nAttrMon's output folder the first output plug to use HTTP, without providing a specific or different port (defaults to 8090) as others will be the first one to start the HTTP server. 

We can configure this HTTP server to use SSL certificates. Here are some examples:

## Using a self-signed generated certificate

Let's start by generating a self-signed certificate (4096 bits valid for 1 year) using the openaf console:

````javascript
> var host = "my_nattrmon_host.domain.local"; // provide the domain for which you are generating the certificate
> ow.loadJava();
> var c = new ow.java.cipher(); // creating a java cipher object
> var kp = c.genKeyPair(4096);  // generate an in-memory 4096 bits key-pair
> var cert = c.genCert("cn=" + host, kp.publicKey, kp.privateKey, new Date(now() + 31536000000), void 0, "/some/folder/ssl.jks", "sslpasswd");
> encryptText
Enter text:
Encrypted text: 797AD06F0FB6E1E6F4B17EB182670494D3EA259B24245847062CCA0C5D023F26
````

And then edit the HTTP plug, for example as 00.http.yaml:

````yaml
output:
  name         : Output HTTP
  description  : >
    Provides a very basic web site for read-only access to the inputs and warnings generated. It uses the nOutput_HTTPJSON to
    retrieve the necessary data when needed.
  execFrom     : nOutput_HTTP
  execArgs     :
     title      : Some title
     keyStore   : /some/folder/ssl.jks
     keyPassword: 797AD06F0FB6E1E6F4B17EB182670494D3EA259B24245847062CCA0C5D023F26
````

Keep in mind that:
* the _keyPassword_ is actually the Java Key Store (jks) password and not related with the key itself
* the hostname provided must match the one used to access through HTTP (that's how Java will figure out the right certificate)

## Using a CA signed certificate

Using the SSL certificate provided follow similar instructions to:

````sh
keytool -import -trustcacerts -file mycert.crt -keystore /some/folder/ssl.jks -storepass sslpasswd
````

Do note that the CA used must be already trusted by your Java setup. If not you need to import the corresponding root certificate also:

````sh
keytool -import -trustcacerts -file root.crt -keystore /some/folder/ssl.jks -storepass sslpasswd
````

And then edit the HTTP plug, for example as 00.http.yaml:

````yaml
output:
  name         : Output HTTP
  description  : >
    Provides a very basic web site for read-only access to the inputs and warnings generated. It uses the nOutput_HTTPJSON to
    retrieve the necessary data when needed.
  execFrom     : nOutput_HTTP
  execArgs     :
     title      : Some title
     keyStore   : /some/folder/ssl.jks
     keyPassword: 797AD06F0FB6E1E6F4B17EB182670494D3EA259B24245847062CCA0C5D023F26
````

Keep in mind that:
* the _keyPassword_ is actually the Java Key Store (jks) password and not related with the key itself
* the hostname provided must match the one used to access through HTTP (that's how Java will figure out the right certificate)

For testing you can use the [InstallCert oPack functionality and the ow.java.setIgnoreSSLDomains function](https://docs.openaf.io/docs/guides/advanced/easily-add-ssl-cert.html).
