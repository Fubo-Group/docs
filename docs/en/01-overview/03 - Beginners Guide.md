---
title: "Beginners Guide"
---

# What is MQTT?

MQTT is a lightweight **publish/subscribe** messaging protocol designed for M2M (machine to machine) telemetry in low bandwidth environments.

It was designed by Andy Stanford-Clark (IBM) and Arlen Nipper in 1999 for connecting Oil Pipeline telemetry systems over satellite.

Although it started as a proprietary protocol it was released Royalty free in 2010 and became an OASIS standard in 2014.

**MQTT** stands for **MQ** Telemetry Transport but previously was known as Message Queuing Telemetry Transport.

**MQTT** is fast becoming one of the main protocols for **IOT** (internet of things) deployments.

# MQTT Versions

There are two different variants of MQTT and several versions.

- MQTT v3.1.0 –
- MQTT v3.1.1 – In Common Use
- MQTT v5 – Currently Limited use
- **MQTT-SN** – See notes later
  
The original **MQTT** which was designed in 1999 and has been in use for many years and is designed for **TCP/IP networks**.

MQTTv3.1.1 is version in common use.

There is very little difference between v3.10 and 3.1.1. Here is a [Github page](https://github.com/mqtt/mqtt.github.io/wiki/Differences-between-3.1.0-and-3.1.1) detailing the main differences

Here is the actual Specification [MQTT V3.1](http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/MQTT_V3.1_Protocol_Specific.pdf) and here is a more detailed overview of the [MQTT protocol packet structure,.](http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/MQTT_V3.1_Protocol_Specific.pdf)

The latest MQTT version(v5) , [has now been approved](https://www.oasis-open.org/news/announcements/mqtt-v5-0-is-an-approved-oasis-committee-specification) (Jan 2018).. You can download the specification [here](http://docs.oasis-open.org/mqtt/mqtt/v5.0/cs01/mqtt-v5.0-cs01.pdf).

If you are wondering what happened to 4 then see [here](http://www.eclipse.org/community/eclipse_newsletter/2016/september/article3.php).

For More Information see [MQTT v 5.0 New Features Overview](http://www.steves-internet-guide.com/mqttv5/)

Here is a [Github page](https://github.com/mqtt/mqtt.github.io/wiki/Differences-between-3.1.1-and-5.0) detailing the main differences between MQTT v3.1.1 and MQTT v5.

## MQTT Version 5 Support Notes:

As of **release 1.6** the mosquitto broker supports MQTT v5 in addition to MQTT v3.11.

You can continue to use older version 3.11 client with the latest broker.

The Paho Python client v 1.5 now supports for v5.

## MQTT-SN Notes

**MQTT-SN** which was specified in around 2013, and designed to work over **UDP**, ZigBee and other transports.

**MQTT-SN** doesn’t currently appear to be very popular. and the specification hasn’t changed for several years, but I expect that to change as IOT deployments start. See MQTT-SN working Notes. for more details on MQTT-SN.

# MQTT Clients
Because MQTT clients don’t have addresses like email addresses, phone numbers etc. you don’t need to assign addresses to clients like you do with most messaging systems.

For MQTTv3.1.1 there is client software available in almost all programming languages and for the main operating systems Linux, Windows, Mac from the [Eclipse Paho project](https://eclipse.org/paho/downloads.php).

- [Paho Python client](http://www.steves-internet-guide.com/into-mqtt-python-client/).
- [Node.js MQTT Client-Starting Guide](http://www.steves-internet-guide.com/using-node-mqtt-client/)
- [JavaScript Websockets Client](http://www.steves-internet-guide.com/using-javascript-mqtt-client-websockets/)
- [C++ Client with Arduino](http://www.steves-internet-guide.com/using-arduino-pubsub-mqtt-client/)

The Paho client v 1.5.1 now supports MQTTv5.0 . Here is a [link](https://www.eclipse.org/paho/index.php?page=downloads.php) to the client comparison chart and download page .


# MQTT Brokers or Servers

> **Note**: The original term was broker but it has now been standardized as Server. You will see Both terms used.

There are many MQTT brokers available that you can use for testing and for real applications.

There are free self hosted brokers , the most popular being [Mosquitto](https://mosquitto.org/) and commercial ones [FuboX](https://www.fubogroup.com).

> **Mosquitto** is a free open source MQTT broker that runs on Windows and Linux.

# MQTT Over WebSockets

**Websockets** allows you to receive MQTT data directly into a web browser.

This is important as the web browser may become the DE-facto interface for displaying MQTT data.

MQTT websocket support for web browsers is provided by the **Javascript MQTT Client**.

See –[Using MQTT Over WebSockets](...)

# MQTT Security

MQTT supports various authentications and data security mechanisms.

It is important to note that these security mechanisms are configured on the MQTT broker, and it is up to the client to comply with the mechanisms in place.

See [An Introduction to MQTT security mechanisms](...)



# Common Questions

If you are familiar with the web and email then you will probably find that MQTT is very different. These are some of the questions I had, and saw on other sites and forums that may clear things up a little.

**Q, What Port does MQTT Normally Use?**
- A: The standard port is 1883.
- Q: Can you use MQTT without a broker?
- A: No See How MQTT works

**Q: What Protocol does MQTT use?**
- A: The standard version uses TCP/IP.

**Q: Can multiple clients publish to the same topic?**
- A: Yes

**Q: Is is possible to know the identity of the client that published a message?**
- A: No not unless the client includes that information in the topic or payload.

**Q: What happens to messages that get published to topics that no one subscribes to?**
- A: They are discarded by the broker.

**Q: How can I find out what topics have been published?**
- A: You can’t do this easily as the broker doesn’t seem to keep a list of published topics as they aren’t permanent.

**Q: Can I subscribe to a topic that no one is publishing to?**
- A: Yes

**Q: Are messages stored on the broker?**
- A: Yes but only temporarily. Once they have been sent to all subscribers they are then discarded. But see next question.

**Q: What are retained messages?**
- A: When you publish a message you can have the broker store the last published message. This message will be the first message that new subscribers see when they subscribe to that topic. MQTT only retains 1 message. See [Understanding Retained Messages]()

# MQTT and Node.js
Many of the example scripts on this site use node.js. Node.js is a good choice for learning MQTT as it is easy to use and readily available. See Using the [mqtt.js](https://www.npmjs.com/package/mqtt).

# MQTT and Node-Red
If you don’t want to program then Node-Red is a Flow based tool that makes it easy to create MQTT projects. See Beginners guide to node-red on my other site to get started.


## MQTT vs HTTP

If you are wondering if MQTT is the best choice for your project then here are a collection of articles comparing MQTT with HTTP.

- [Internet of Things: Battle of The Protocols (HTTP vs. Websockets vs. MQTT)](https://www.linkedin.com/pulse/internet-things-http-vs-websockets-mqtt-ronak-singh-cspo/)
- [MQTT vs. HTTP: which one is the best for IoT?](https://medium.com/mqtt-buddy/mqtt-vs-http-which-one-is-the-best-for-iot-c868169b3105)
- [HTTP vs MQTT performance tests](https://flespi.com/blog/http-vs-mqtt-performance-tests)
- [MQTT and HTTP : Comparison between two IoT Protocols](https://iotdunia.com/mqtt-and-http/) – contains errors but chart useful
- [MQTT-SN vs Coap for robotics](https://ac.els-cdn.com/S1877050915038193/1-s2.0-S1877050915038193-main.pdf?_tid=e7fee893-ce0a-4248-81a2-49c217bba80b&acdnat=1528810161_370e74a0dccf229a74ac88ddebbbe9b9)

# Real World MQTT Example Deployments
It’s often useful and interesting to see how a particular technology is actually being used. Here are some examples I’ve come across:

- [Freight Farms-MQTT](https://blog.heroku.com/freight-farms-on-heroku)
- [IP pinger swarm using MQTT](http://plumbery.readthedocs.io/en/latest/tutorial.mqtt.pinger.swarm.html)
- [How do you use MQTT- Mosquitto.org](https://mosquitto.org/2012/01/do-you-use-mqtt/)
- [Owntracks](http://owntracks.org/booklet/guide/whathow/) -Location tracking
