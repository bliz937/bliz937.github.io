---
layout: post
title:  "WCF Existing connection closed"
date:   2017-04-25 12:00:00 +0200
description: "WCF - An existing connection was forcibly closed by the remote host"
tags: windows network wcf reset visual studio visualstudio exception
---

Building an application that uses WCF, I ran into an issue that was similar to [WCF Connection Reset](/2017/02/08/WCF-connection-reset.html) but was caused by something else.

## My problem

The exception thrown by the application was

```
ERROR - The socket connection was aborted.
This could be caused by an error processing your message or a receive timeout being exceeded
by the remote host, or an underlying network resource issue.

ERROR - The read operation failed, see inner exception.

ERROR - The socket connection was aborted. This could be caused by an error processing your
message or a receive timeout being exceeded by the remote host, or an underlying network
resource issue.

ERROR - An existing connection was forcibly closed by the remote host
```

## My fix

As in the [WCF Connection Reset](/2017/02/08/WCF-connection-reset.html) post, I turned on tracing.

The first exception encountered being
```
System.ServiceModel.CommunicationObjectAbortedException
```
with the message
```
The socket connection has been disposed
```

Followed by
```
System.ObjectDisposedException
```

with the message
```
Cannot access a closed Stream
```

Going back to my server code, my method looked similar to this
```csharp
public Stream GetStuff()
{
  ...
  ...

  using (MemoryStream ms = new MemoryStream())
  {
    ...
    ...
    return ms;
  }
}
```

Which I realised was a mistake after coming across [WCF streaming who closes the file](http://stackoverflow.com/questions/13384886/wcf-streaming-who-closes-the-file#answer-13399154) question.

Removing the ```using``` and replacing it with the ```OperationCompleted``` event fixed the problem.

Alternatively, wrap the call at client side with ```using```, E.g.
```csharp
using(Stream stuff = server.GetStuff())
{
  ...
  ...
}
```
