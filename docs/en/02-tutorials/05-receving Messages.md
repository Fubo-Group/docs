---
title: "Receving Messages"
---

Messages are received by processing the **message event**.The format is:

`function (topic, message, packet) {}`

Example code is shown below:

```js
//handle incoming messages
client.on('message',function(topic, message, packet){
	console.log("message is "+ message);
	console.log("topic is "+ topic);
	console.log("packet =" +JSON.stringify(packet));
	console.log("packet retain =" +packet.retain);
});
```

The messages contains the text messages and the packet object contains the message as a buffer object in addition to message properties like **retain flag** etc as shown below:

```js
message is {"volts":240}
topic is testtopic
packet ={"cmd":"publish","retain":false,"qos":0,"dup":false,"length":27,"topic":"testtopic","payload":{"type":"Buffer","data":[123,34,118,111,108,116,115,34,58,50,52,48,125]},"properties":{"subscriptionIdentifier":1}}
```

# Example Code

The following code is part of the on.message() function.

## Detect if message is retained

```js
console.log("packet retain =" +packet.retain);
if(packet.retain)
console.log("message is a retained message");
else
console.log("message is not retained message");
```

## Extract the subscription Identifier from the message

```js
console.log("subscription Identifier= "+packet.properties.subscriptionIdentifier)
```

## Work with a Received JSON Payload
To work with a received JSON payload we need to first convert the payload into a JavaScript object using:

```js
let message=JSON.parse(message);
```

Example our received message is

```js
message is {“volts”:240}
```

to get volts we do the following

```js
let message=JSON.parse(message);
let volts=message.volts;
```

Because the payload may not be JSON we will need to test it. The easiest way is using a try catch sequence (ref) as shown below:

```js
try
{
message=JSON.parse(message);
let volts=message.volts
console.log("volts="+volts);
}
catch(err)
{
	console.log("not JSON");
}
```