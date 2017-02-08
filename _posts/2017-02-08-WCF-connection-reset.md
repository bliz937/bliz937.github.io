---
layout: post
title:  "WCF Connection Reset"
date:   2017-02-08 12:00:00 +0200
description: "WCF Connection Reset - An existing connection was forcibly closed by the remote host"
tags: windows network wcf reset visual studio visualstudio exception
---

Trying to build a <a href="https://msdn.microsoft.com/en-us/library/ms731082(v=vs.110).aspx">WCF</a> client and server application, I ran into an issue which would show itself as an exception on the client application.

## My problem

In Visual Studio, the exception tree is the following:

```
The socket connection was aborted. This could be caused by an error processing your message or
a receive timeout being exceeded by the remote host, or an underlying network resource issue.
Local socket timeout was '10675199.02:48:05.4775807'.
```
And the inner exception
```
An existing connection was forcibly closed by the remote host
```

Somewhere I read that exceptions by the client and server appear as an exception in the client.
Also, because WCF has security in mind, by default, exceptions are not descriptive when occured in the server.

## My fix

My first approach was to set ```IncludeExceptionDetailInFaults``` as true in the ```ServiceBehavior``` attribute to [return unhandled exceptions](https://msdn.microsoft.com/en-us/library/ff649234.aspx) as can be seen above.

```cs
[ServiceBehavior(IncludeExceptionDetailInFaults = true)]
  public class WCFServer : IWCFServer
```

The exception showed that the connection was being reset.
Some reasons for this was provided by an [answer](http://stackoverflow.com/questions/2582036/an-existing-connection-was-forcibly-closed-by-the-remote-host#answers) on StackOverflow.

I then used [WireShark](http://www.wireshark.org/) to have a look at the packets that the client and server apps were sending. My filter was ```tcp.port == 5000``` - I listened on TCP port 5000 in the server application.

![WireShark capturing packets](/assets/2017/02/wcf-wireshark.png)

The above shows some malformed packets being sent, but on an unknown protocol to me.
This wasn't very helpful. However, in the last packet from the server, I can see that the network connection is indeed being reset with the [RST](http://packetlife.net/blog/2011/mar/2/tcp-flags-psh-and-urg/) flag.

Next, I enabled tracing on the server application with the help of this Youtube video: https://www.youtube.com/watch?v=fXSjwBgRrto.

Running the WCF applications again and looking at the tracing result showed

![Tracing log result](/assets/2017/02/wcf-tracing.PNG)

Looking at the 'Replying to an operation threw an exception' activity; on the XML tab, in the ```ExceptionString```, I got
```System.Runtime.Serialization.InvalidDataContractException: No set method for property Info```.

And another [StackOverflow](http://stackoverflow.com/questions/2323277/wcf-chokes-on-properties-with-no-set-any-workaround#answer-2323289) post revealed that the ```DataMember``` has to have a set method even if you don't actually set it.

Going to my class, that has the ```[DataContract]``` attribute, I then could do two things to overcome the above issue:

1) Add a ```private set``` to the property that has the ```[DataMember]``` attribute

2) Move the ```[DataMember]``` attribute to the private field instead

I have come across other solutions relating to the connection being reset, but in this instance, this was the solution that worked for me.
