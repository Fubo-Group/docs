---
title: Node.js MQTT Client Guide
---


The node.js MQTT client is an open source client that can be used for publishing messages and subscribing to topics on an MQTT broker. It  can be installed using:

```sh
npm install mqtt --save 

# and
npm install mqtt -g
```

To install the command line tools

Documentation for the client is available [here](https://www.npmjs.com/package/mqtt#store)

In this tutorial we cover the important client functions and create a simple publish subscribe node,js example script.

To use the client you need to use:

```js
var mqtt=require('mqtt');
```

at the top of your script.

# Overview

**Node.js** scripts are asynchronous and use events. Events are triggered when something happens e.g the client connects.

You create a listener for the events you are interested in and this listener calls a callback function to process the event.

# The Connection Method

This is the main method, and when called it connects to an MQTT broker and returns a client class.

The method is called as follows:

```js
var client = mqtt.connect(url,options)
```
e..g.

```js
var client = mqtt.connect("mqtt://192.168.1.157",options)
var client = mqtt.connect("mqtt://192.168.1.157",{clientId:"mqttjs01"})
```

Usually they are many options that need to be passed to the method and so the options is usually created as a JavaScript object. E.G.

To use user name and password authentication and a clean session use the following options.

```js
options={
clientId:"mqttjs01",
username:"steve",
password:"password",
clean:true};
```

The **MQTT protocol** acknowledges a connection with the **CONNACK** message.

This raises the **on_connect** event in the client which can be examined by creating a listener as follows:

```js
client.on("connect",function(){	
console.log("connected");
```

The listener waits for the connect event and calls a callback function; which in the example simply prints a message when the client connects.

We can now try our first example script.


This script connects to the broker.fubogroup.com MQTT broker and prints a message when connected.

```js
var mqtt    = require('mqtt');
var client  = mqtt.connect("mqtt://broker.fubogroup.com",{clientId:"mqttjs01"});
client.on("connect",function(){	
console.log("connected");
})
```

The Interesting thing to note is that the cursor of the command prompt is on a new line and blinking.



simple-connect-node-script


This is because the client is **still connected** to the broker and is sitting in an **event loop**.

However we are at the end of the script so the only thing that can happen is an event. So our script just sits there and does nothing.

You can terminate the script by closing the client connection using.

```js
client.end();
```

The **on_connect** event also sets a flag called **connected** to **true**. You can access this flag using: client.connected.

I’ve modified our simple script to print this flag before and after:

```js
var client  = mqtt.connect("mqtt://broker.fubogroup.com",{clientId:"mqttjs01"});
console.log("connected flag  "+client.connected);
client.on("connect",function(){	
console.log("connected  "+client.connected);
})
```

If you run the script you should see the following:

node-connection-script-2

# Connection Failures

What happens if the connection fails?

The node client provides the error event for a connection failure. So to monitor this we need to create a listener for this event as follows:

```js
client.on("error",function(error){ console.log("Can't connect"+error);
```

Unfortunately this will only catch failures like authentication failures.

If you try to connect on the wrong port or the wrong address **no error** is generated and you will find the client sits there and attempts to reconnect.

If the error detects an authentication failure then you will need to quit otherwise the client will continually attempt to connect.

Therefore the on_error event listener should be something like the one below:

```js
client.on("error",function(error){
console.log("Can't connect" + error);
process.exit(1)});
```

# Publishing Messages

To publish a message use:

```js
client.publish(topic, message, [options], [callback])
```

the options and callback parameters are optional. Options include retain message flag,qos etc.
e.g.

```js
client.publish("testtopic", "test message")
```

and

```js
var options={
retain:true,
qos:1};
client.publish("testtopic", "test message",options)
```

Before publishing it is wise to check that you are connected using:

```js
if (client.connected==true){
client.publish("testtopic", "test message",options)
}
```

> However this is unlikely to work as the if statement will probably be tried before the client is connected as the connection is asynchronous.

Therefore if you want to simply- connect publish and disconnect, then you should call the publish in the **on_connect callback**.

If you need to publish at regular intervals e.g when sending status information then use the `setInterval` function as shown below.

```js
var message="test message";
var topic="testtopic";
//publish every 5 secs
var timer_id=setInterval(function(){publish(topic,message,options);},5000);

//publish function
function publish(topic,msg,options){
  console.log("publishing",msg);
if (client.connected == true){
  client.publish(topic,msg,options);
}
```

# Subscribing to Topics

To subscribe use the **client.subscribe** method. The general format is:

```js
client.subscribe(topic/topic array/topic object, [options], [callback])
```

You use a topic **string method** if you only want to subscribe to a single topic.

You use the **array method** if you want to subscribe to multiple topics with the same qos. i.e. the qos you pass in the options is for all topics in the array.

You use the **object method** when you want to subscribe to multiple topics with different **qos** settings.
Example usage:

```js
var topic_s="topic";
var topic_list=["topic2","topic3","topic4"];
var topic_o={"topic22":0,"topic33":1,"topic44":1};
client.subscribe(topic_s,{qos:1});
client.subscribe(topic_list,{qos:1});
client.subscribe(topic_o);
```

# Receiving Messages
Before you can receive any messages you will need to be subscribed to a topic.

The message event is triggered when a new message arrives.

To process this event you need to **create a listener** .e.g.

```js
client.on('message',function(topic, message, packet){
	console.log("message is "+ message);
	console.log("topic is "+ topic);
});
```

The callback needs to accept three parameters.-topic,message and packet.

The packet object contains the message,topic and message details like QOS,retain etc.

To see its contents use:

```js
console.log(JSON.stringify(packet)
```

Below is a screen shot of an example received packet:

nodejs-received-packet

 

If we want to look at the retain flag we use:

```js
console.log("packet retain =" +packet.retain);
```

To print the topic, message,packet and retain use the following code:

```js
client.on('message',function(topic, message, packet){
	console.log("message is "+ message);
	console.log("topic is "+ topic);
	console.log("packet =" +JSON.stringify(packet));
	console.log("packet retain =" +packet.retain);
});
```

# Final Script Example

The final script will connect to a broker subscribe to a collection of topics and publish a message on one of those topics every x seconds.

It will print out received messages to the console and quit after two loops.

```js
var mqtt    = require('mqtt');
var count =0;
var client  = mqtt.connect("mqtt://192.168.1.157",{clientId:"mqttjs01"});
console.log("connected flag  " + client.connected);

//handle incoming messages
client.on('message',function(topic, message, packet){
	console.log("message is "+ message);
	console.log("topic is "+ topic);
});


client.on("connect",function(){	
console.log("connected  "+ client.connected);

})
//handle errors
client.on("error",function(error){
console.log("Can't connect" + error);
process.exit(1)});
//publish
function publish(topic,msg,options){
console.log("publishing",msg);

if (client.connected == true){
	
client.publish(topic,msg,options);

}
count+=1;
if (count==2) //ens script
	clearTimeout(timer_id); //stop timer
	client.end();	
}

//////////////

var options={
retain:true,
qos:1};
var topic="testtopic";
var message="test message";
var topic_list=["topic2","topic3","topic4"];
var topic_o={"topic22":0,"topic33":1,"topic44":1};
console.log("subscribing to topics");
client.subscribe(topic,{qos:1}); //single topic
client.subscribe(topic_list,{qos:1}); //topic list
client.subscribe(topic_o); //object
var timer_id=setInterval(function(){publish(topic,message,options);},5000);
//notice this is printed even before we connect
console.log("end of script");
```

Here is a screen shot of the script being run:

node-mqtt-script-example

You should notice that the end of script message is printed even though the script is obviously running.

This illustrates the asynchronous nature of **node.js**.

# Using SSL
You can secure the connection using SSL. Below is a simple connection script that connects to a broker using SSL:

```js
var mqtt    = require('mqtt');
const fs = require('fs');
var caFile = fs.readFileSync("ca.crt");

var options={
clientId:"mqttjs01",
//port:8883,
//host:'192.168.1.71',
//protocol:'mqtts',
rejectUnauthorized : false,
ca:caFile 
}
var client  = mqtt.connect("mqtts://192.168.1.71:8883",options);
console.log("connected flag  " + client.connected);
client.on("connect",function(){	
console.log("connected  "+ client.connected);
})
```

**Notes:** The **rejectUnauthorized** : false option is only necessary if there is a domain mismatch on the certificate and should normally not be used.

Notice how the certificate file is first read and passed as an option.

If you are also using client certificates then use the following:

var KEY = fs.readFileSync(‘client-certs\\client.key’);
var CERT = fs.readFileSync(‘client-certs\\client.crt’);

and in options:

key: KEY,
cert: CERT,

Here is a working connect SSL example script:

//https://github.com/mqttjs/MQTT.js/issues/264
var mqtt    = require('mqtt');
const fs = require('fs');
//var cert="ca.crt";
var caFile = fs.readFileSync("ca.crt");
//if using client certificates
var KEY = fs.readFileSync('client-certs\\client.key');
var CERT = fs.readFileSync('client-certs\\client.crt');
//
var options={
clientId:"mqttjs01",
//port:8883,
//host:'192.168.1.71',
//protocol:'mqtts',
rejectUnauthorized : false,
//if using client certificates
key: KEY,
cert: CERT,
//
ca:caFile 

}
var client  = mqtt.connect("mqtts://192.168.1.71:8883",options);
console.log("connected flag  " + client.connected);
client.on("connect",function(){	
console.log("connected  "+ client.connected);
})
See Github reference for more details