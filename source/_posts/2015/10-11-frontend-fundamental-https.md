---
layout: post
title: "Frontend Fundamental - HTTPS"
date: 2015-10-11T15:30:50+08:00
tags: [HTTP/HTTPS]
---

# What is HTTPS?
HTTPS stands for HTTP Secure(or HTTP over TLS, SSL). The purpose is to establish a encrypted connection which provides more protection as opposed to HTTP that only creates transparent connections that are under the risk of being eavesdropping or intercepted by man-in-the-middle.

HTTPS differs from HTTP at some points -

* HTTPS uses port 443 as default, while HTTP uses port 80.
* HTTPS still uses HTTP as its essential communication method. You can take SSL/TLS as an "upgraded connection" as opposed to "normal connection" which HTTP uses with.

# What is TLS/SSL?
SSL stands for Secure Sockets Layer, and TLS stands for Transport Layer Security. They are both cryptographic protocol making network communication more secure.

SSL is the predecessor to TLS. SSL was started by Netscape, and was upgraded to TLS in 1999 because Netscape was no more and thus no further maintenance from then on.

Today we still refer to TLS as SSL, because historically they are the same thing, only name changed.

# How does it work?
Generally, TLS/SSL works with **private key** ,**public key** and **certificate**.

* Client and server have its own **private keys**. **Public key** is the same on both client and server.
* **Certificate** carries **public key** and other necessary informations.
* **Certificate** can exist on both client and server.
* Who sends **certificate** depends on method.

In order to use HTTPS, website owner should do the following:

1. Generate **Certificate Signing Request (CSR)**. By doing this, your computer will generate a **private key**.
2. Go to a trusted third party **Certificate Authority (CA)**, and validate with your **CSR**.
3. When validation ends, **CA** will give you a new **public key** encrypted with **CA's private key**.
4. Install this **CSR** on your server machine.

Client and server establish connection by using a handshaking procedure:

#### tl;dr Version

In case you are impatient like me...

1. Server sends **server's public key (certificate)** to client.
2. Client checks if it's **expired** or **not listed on the browser's certificates authority list**.
3. If this certificate is trusted, client will send back its **client's public key** to server.
4. Server creates an unique hash and encrypts it with **client's public key** and **server's private key** and send it to client.
5. Client decrypts the hash with **client's private key** and **server's public key**, and verifies it.
6. Server and client start to communicate in cryptographic communication.

#### Basic TLS Handshaking

1. First, client and server must agree on both using TLS/SSL protocol (HTTPS).
2. Client sends a **Client Hello** message. In this step, client also sends informations like *the highest TLS version it supports*, *cipher suites* and *suggest compression methods*.
3. Server responds with a **Server Hello** message, containing *the chosen TLS version*, *a random number*, *the choices of cipher suites and compression method* which client offered.
4. Server sends a **Certificate** to client.
5. Server sends a **Server Key Exchange** message to client.
6. Server sends a **Server Hello Done** message to client.
7. Client responds with **Client Key Exchange**, which may contains with *PreMasterSecret*, *public key* or *nothing*(depends on cipher suites choice).
8. The client and server will now use *random number* and *PreMasterSecret* to generate a *MasterSecret*, which will be used by the following key data.
9. Client sends a **Change Cipher Spec** message back to server. That means, "Everything I tell you now on will be authenticated."
10. Client sends a encrpted **Finished** message. Server will attempt to decrpt the **Finished** message. If it fails, the connection will torn down.
11. Server sends a **Change Cipher Spec** message to client, telling "Everything I tell you now on will be authenticated."
12. Server will do the same thing like what client does in step 9.

#### Client-Authenticated TLS Handshaking

The procedure is mostly identical to basic TLS handshaking, but with some changes from step 4:

4. Server sends its own **Certificate** to client.
5. Server sends **Server Key Exchange**.
6. Server requests client's **Certificate**.
7. Server sends **Server Hello Done**.
8. Client responds with its **Certificate**.
9. Client sends **Client Key Exchange**.
10. Client sends a **Certificate Verify** message, which is a signature over the previous message using **client's private key**. Later will be verified by server with **client's public key**.
11. After this step is identical to step 9 of basic TLS handshaking.

#### Cipher Suites

You may wonder what is **cipher suites**, right? Here's what it defines:

* **Key** exchange algorithm - how client and server authenticate during connection.
  * RSA, Diffie-Hellman, ECDH, SRP, PSK.
* **Encryption** algorithm - the method to encrypt message stream.
  * RC4, Triple DES, AES, IDEA, DES...
* Message authentication code(MAC, some refer to hash) - the way to create a cryptographic hash.
  * For TLS: HMAC-MD5, HMAC-SHA.
  * For SSL: SHA, MD5, MD4, MD2....

# Pros and Cons
Pros

* Security, security and security! No one will risk exposing their password, right?

Cons

* Complex handshaking means more bandwidth overhead.
* Since it costs more bandwidth, you may not want to use HTTPS on every page of your site, and by separating our protocol into two may lead more complexity to our system architecture.

# Acquiring Certificates
Acquiring a valid certificate means you need to purchase it issued by 3rd party Certificate Authority.

There are tons of CA on the Web, and since this article is not about getting your best deal SSL certificate, I will use `openssl` to generate a self-signed certificate for the following demonstration, and remember **self-signed certificate is for testing/development purpose**. Don't ever use it in production, or you will scare customers away.

#### Generate a Private Key

`openssl req -nodes -new -x509 -keyout server.key -out server.cert`

Here I use `req` command to generate a certificate and a private key under current directory.

By doing this, the following form will prompt out. Type whatever it requests, and remember to leave `challenge password` empty.

```plain
Country Name (2 letter code) [AU]:TW
State or Province Name (full name) [Some-State]:Taiwan
Locality Name (eg, city) []:Taipei
Organization Name (eg, company) [Internet Widgits Pty Ltd]:meow
...
```

# HTTPS In Practice

Server

```js
var https = require("https");
var fs = require("fs");

var options = {
  key: fs.readFileSync("./server.key"),
  cert: fs.readFileSync("./server.cert")
};

https.createServer(options, function(req, res){
  res.writeHeader(200, {
    "content-type": "text/plain",
  });
  res.end("Hello World");
}).listen(3000);

console.log("Server is running...");
```

Let's proceed with Chrome...

![https-big](/images/https-big.png)

Of course, our certificate is not issued by authorized 3rd party CA, and thus not trusted.

![https-small](/images/https-small.png)

At least, it works with our Node.js, yay~

# References

* [https on wikipedia](https on wikipedia)
* [TLS on wikipedia](TLS on wikipedia)
* [Public-key cryptography](Public-key cryptography)
* [https and certification for dummies on Stackoverflow](https and certification for dummies on Stackoverflow)
* [The First Few Milliseconds of an HTTPS Connection](The First Few Milliseconds of an HTTPS Connection)
* [How SSL Works](How SSL Works)
* [Creating a Self-Signed SSL Certificate](Creating a Self-Signed SSL Certificate)
* [How SSL works tutorial - with HTTPS example](How SSL works tutorial - with HTTPS example)
* [What is HTTPS?](What is HTTPS?)
* [Is it possible to generate RSA key without pass phrase?](Is it possible to generate RSA key without pass phrase?)

[https on wikipedia]: https://en.wikipedia.org/wiki/HTTPS
[TLS on wikipedia]: https://en.wikipedia.org/wiki/Transport_Layer_Security
[Public-key cryptography]: https://en.wikipedia.org/wiki/Public-key_cryptography
[https and certification for dummies on Stackoverflow]: http://serverfault.com/questions/429375/https-and-certification-for-dummies
[The First Few Milliseconds of an HTTPS Connection]: http://www.moserware.com/2009/06/first-few-milliseconds-of-https.html
[How SSL Works]: https://ssl.trustwave.com/support/support-how-ssl-works.php
[Creating a Self-Signed SSL Certificate]: https://devcenter.heroku.com/articles/ssl-certificate-self
[How SSL works tutorial - with HTTPS example]: https://www.youtube.com/watch?v=iQsKdtjwtYI
[What is HTTPS?]: https://www.youtube.com/watch?v=JCvPnwpWVUQ
[Is it possible to generate RSA key without pass phrase?]: http://serverfault.com/questions/366372/is-it-possible-to-generate-rsa-key-without-pass-phrase
