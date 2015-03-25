# XJCP2 – Specification

This protocol specification is designed after having the original xjcp working for about a year and should fix known issues.

XJCP stands for Extensible Json Chat Protocol. The main goal of the protocol is to specify a minimal communication base that can be extended by more modules.

## Table of Content

1. Communication Channel
2. JSON-Objects
3. In-Out Types Overview
4. I/O Details
5. Helper-Classes
6. Batch-Processing
7. Multi Server Support

## Communication Channel

The communication is done via https.
Some servers and clients may support and use ssl-sockets instead to be more efficient.
However all clients and servers must support https as a fallback when no socket is supported.

Http and sockets that are not secured by ssl are not allowed.

You may send data to the server as POST or GET variable „msg“ for the normal protocol or „m“ for the minified protocol. When both variables are declared the normal protocol will be prefered and the minified protocol ignored.

When using a socket connection a package is one line starting with "msg:" for the normal protocol or "m:" for the minified protocol followed by the actual package.
Lines starting with other prefixes can be used for binary data that can be addressed in xjcp (more later).

With the http variant the server does only answer requests.
But when evets are due, they will be send with the next response no matter what the request was.

Socket connections behave differently they send events just when they occur independant of client requests.
Everything else works as normal response like with https.

Therfore, it is recommended that https clients do a clever ping loop to get events and try to handle events on each packet they receive.

### Examples

Assume you want to send the following simple packet with no data in it.
```json
{"id":"<id>"}
```

Https requests with GET
```
https://www.yourserver.de/xjcp2.php?msg=%7B"id"%3A"<id>"%7D
```
To encode the data correctly in c# for example simply use
```cs
json = "{\"id\":\"<id>\"}";
uri = "https://www.yourserver.de/xjcp2.php?msg=" + Uri.EscapeDataString (json);
```

As https POST request at https://www.yourserver.de/xjcp2.php the post data is
```
msg=%7B"id"%3A"<id>"%7D
```

Using a ssl-socket connection you should send (or expect)
```
msg:{"id":"<id>"}\n
```

## JSON-Objects

The minified protocol works the same as the normal protocol, only qualifiers are shortened. A list with the minified types can be found in in-out-types section.
