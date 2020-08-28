---
layout: post
title: "Port 80 is being listened by SYSTEM (PID 4) on Windows"
description: ""
tags: 
---

This is a famous problem.

* [windows 7 - Why is System process listening on Port 80? - Super User](https://superuser.com/q/43307/424146)
* [iis - Port 80 is being used by SYSTEM (PID 4), what is that? - Stack Overflow](https://stackoverflow.com/q/1430141/2691131)
* [Opening Up Port 80 For Apache to Use On Windows](https://web.archive.org/web/20181004143631/http://www.devside.net/wamp-server/opening-up-port-80-for-apache-to-use-on-windows)

And, a Microsoft engineer Raymond Chen answered here.

* [How can I determine why the System process is listening on port 80? \| The Old New Thing](https://devblogs.microsoft.com/oldnewthing/20180703-00/?p=99145)


Here is the result of `netstat -abo`:

~~~
(...)
  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:80             myhostname:0          LISTENING       4
 Can not obtain ownership information
(...)
  TCP    [::]:80                myhostname:0          LISTENING       4
 Can not obtain ownership information
(...)
~~~

Use `netstat -ao | findstr :80` for concise result.

HTTP.sys implements [HTTP Server API](https://docs.microsoft.com/en-us/windows/win32/http/http-api-start-page) to listen the ports.
There are two ways to work around the issue.

## Disable all services which are using HTTP.sys

Get the status of which services are using HTTP Server API by invoking `netsh http show servicestate`.
In the following example, pid 2668 is registering `HTTP://+:80/116B50EB-ECE2-41AC-8429-9F9E963361B7/`.

~~~
Snapshot of HTTP service state (Server Session View):
-----------------------------------------------------

Server session ID: FF00000320000001
    Version: 2.0
    State: Active
    Properties:
        Max bandwidth: 4294967295
        Timeouts:
            Entity body timeout (secs): 120
            Drain entity body timeout (secs): 120
            Request queue timeout (secs): 120
            Idle connection timeout (secs): 120
            Header wait timeout (secs): 120
            Minimum send rate (bytes/sec): 150
    URL groups:
    URL group ID: FE00000340000001
        State: Active
        Request queue name: Request queue is unnamed.
        Properties:
            Max bandwidth: inherited
            Max connections: inherited
            Timeouts:
                Entity body timeout (secs): 120
                Drain entity body timeout (secs): 0
                Request queue timeout (secs): 0
                Idle connection timeout (secs): 0
                Header wait timeout (secs): 0
                Minimum send rate (bytes/sec): 0
            Authentication Configuration:
                Authentication schemes enabled:
                    Negotiate
            Number of registered URLs: 1
            Registered URLs:
                HTTP://+:80/116B50EB-ECE2-41AC-8429-9F9E963361B7/

Request queues:
    Request queue name: Request queue is unnamed.
        Version: 2.0
        State: Active
        Request queue 503 verbosity level: Basic
        Max requests: 1000
        Number of active processes attached: 1
        Process IDs:
            2668
~~~

Here is the excerpt of `netsh http show urlacl`.

~~~
    Reserved URL            : http://+:80/116B50EB-ECE2-41ac-8429-9F9E963361B7/
        User: NT AUTHORITY\NETWORK SERVICE
            Listen: Yes
            Delegate: No
            SDDL: D:(A;;GX;;;NS)
~~~

Then, google the keywords and disable all services.

## Change listening addresses of HTTP.sys

By default, HTTP.sys listens `0.0.0.0` and `[::]`, which means all IPv4 and IPv6 addresses.
For example, by invoking `netsh http add iplisten 127.0.0.2`, you can change it to only IPv4 127.0.0.2.
`netsh http show iplisten` reports the current status.
You can restore the default setting by `netsh http delete iplisten 127.0.0.2`.

* [HTTP Server API: IP Listen List](https://docs.microsoft.com/en-us/windows/win32/http/ip-listen-list)
* [Netsh commands for HTTP: add iplisten](https://docs.microsoft.com/en-us/windows/win32/http/add-iplisten)
