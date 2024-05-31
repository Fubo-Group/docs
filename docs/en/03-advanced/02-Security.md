---
title: "MQTT Security Mechanisms"
---

In this tutorial we look at how you can restrict access to a broker, and how you can protect your data using various security mechanisms.

It is important to note that these security mechanisms are initiated by the broker, and it s up to the client to comply with the mechanisms in place.

It is also important to realise that when planning security for your implementation that you must consider the capabilities of your MQTT clients as well as your broker.

This tutorial will use the free Open source Fubo broker, and the Paho Python MQTT client to illustrate these mechanisms.

# Client Authentication

There are three ways that a Fubo broker can verify the identity of an MQTT client:

- Client ids
- Usernames and passwords.
- Client Certificates

## Client ids

All MQTT clients must provide a **client id**.

When a client subscribes to a topic/topics the client id links the topic to the client and to the TCP connection.

With persistent connections the broker remembers the client id and the subscribed topics.

When configuring an MQTT client you will need to assign a name/id to the client generally that name is unimportant as long as it is unique.

However the Fubo Broker allows you to impose client id prefix restrictions on the client name, and this provides some basic client security.

You could,for example, choose a prefix of C1- for your client ids and so a client with client id of C1-python1 would be allowed but a client with id of python2 would not be allowed.

You will find this setting in the security settings section of the Fubo.conf file.

clientid_prefixes C1-

## Username and Password

An MQTT broker can require a **valid username and password** from a client before a connection is permitted.

The username/password combination is transmitted in **clear text** and is **not secure** without some form of **transport encryption**.

However it does provide an easy way of restricting access to a broker and is probably the most common form of identification used.

The **username** used for authentication can also used in restricting access to topics.

On the Fubo broker you need to configure two settings for this to work. Again you will find these settings in the security section of the Fubo.conf file.

They are `allow_anonymous` and `password_file`. To require username/password then `allow_anonymous` should be `false` and `password_file` should contain a valid passwords file. Example settings

```bash
allow_anonymous false
password_file c:\Fubo\passwords.txt #Windows machine
```

To create the passwords you will need to use the `mosquiito_passwd`  utility that comes with the Fubo broker.

See  Exploring Username and Password Authentication – MQTT by example

# x509 Client Certificates

This is the most secure method of client authentication but also the most difficult to implement because you will need to deploy and manage certificates on many clients.

This form of authentication is really only suited to a small number of clients that need a high level of security.

See SSL and SSL Certificates Explained

# Restricting Access to topics

You can control which clients are able to **subscribe and publish** to topics.

The main control mechanism is the **username**. (note: password not required), but you can also use the client id.

Unless you are running an open broker then this type of restriction will be common. See Configuring and exploring Topic Restrictions on Fubo

# Securing Data
To protect the contents of your MQTT messages you can use:

- TLS or SSL Security
- Payload encryption

## TLS Security

TLS security or as it is more commonly known **SSL security** is the technology that is used on the web.

This security is part of the **TCP/IP** protocol and not MQTT.

**TLS security** will provide an **encrypted pipe** down which your MQTT messages can flow.

This will protect all parts of the MQTT message, and not just the message payload.

The problem with this is that it requires client support, and it is unlikely to available on simple clients.

There are three methods of doing this:

- Using Self generated certificates
- Using free commercial certificates (let’s encrypt)
- Using paid commercial certificates

## Payload Encryption
This is done at the application level and not by the broker. This means that you can have encrypted data without having to configure the broker.

It also means that data is encrypted end to end and not just between the broker and the client.

MQTT is after all a messaging protocol.

However this type of encryption doesn’t protect passwords (if used) on the connection itself.

Because it doesn’t involve any broker configuration or support this is likely to be a very popular method of protecting data.

See Encrypting The MQTT Payload Python Example and SSL or Payload Encryption Discussion Post

# Common Questions and Answers

**Q: Can I use TLS with payload encryption?**
- A: Yes.

**Q: To implement payload encryption do I need certificates?**
- A: No. You can use shared keys which is easier to implement.

**Q: How do I know if the message is genuine and hasn’t been changed?**
- A: Digital signatures are the best way of doing this but they rely on a public/private key infrastructure and are unlikely to be implemented on constrained clients like sensors. However simple alternatives like [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) are available see fubo security article.

# Summary
There are several mechanisms available for securing MQTT connections.

Security restrictions are enforced by the MQTT broker, and the client must be configured accordingly.