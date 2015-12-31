---
layout: post
title: "Dynamic Web Proxy Configuration with Firefox and SSH"
date: 2015-12-31 13:30:32 +0000
comments: true
categories: 
---

In my day-to-day life I need to access a number of different intranet web
services. Some of these are on my local work intranet, but I also need to be
able to get to things like temporary services hosted on Grid'5000. These
websites are not available on the public Internet so the standard procedure is
to connect to a gateway that sits at the edge of the intranet and route traffic
through that gateway through either a VPN or SSH socks proxy. This solution
isn't ideal, as it requires setting up the connection and possibly
reconfiguring my browser whenever I want to access a site on one of these
intranets. I can't access work and Grid'5000 systems at the same time, and,
when traffic is being routed through Grid'5000, I can't access sites on the
public Internet (this was a big problem when trying to troubleshoot a web
application with my teammates by screen sharing over Google talk).

So, the first step is to figure out how to appropriately route only the traffic
that needs to go to an intranet site to the right gateway proxy. The
preferences dialog in Firefox is fairly limited, but fortunately the browser
also supports [proxy.pac](http://findproxyforurl.com/) configuration files.
The proxy.pac standard is intended to support automatic discovery of required
proxies for browsers hosted on restrictive intranets, and much of what is
written about it assumes that the file will be hosted on a remote site and
accessed via http, but Firefox can also be configured to just read a static
local file using the file:// url schema.

The proxy.pac file is written in javascript with a few convenience functions
available, a full description can be found at the website
[http://findproxyforurl.com](). Mine looks something like this:

~~~ {.javascript}
function FindProxyForURL(url, host) {

    if (dnsDomainIs(host, "localhost") ||
        isInNet(host, "127.0.0.0", "255.0.0.0") ||
        isInNet(host, "172.17.0.0", "255.255.0.0")) {
        return "DIRECT;";
    }

    if (dnsDomainIs(host, ".grid5000.fr") || 
        isInNet(host, "10.156.0.0", "255.252.0.0")) {
        return "SOCKS5 localhost:9060;";
    }

    if (dnsDomainIs(host, ".ic.ac.uk") ||
        dnsDomainIs(host, ".imperial.ac.uk")) {
        return "SOCKS5 localhost:9070; DIRECT;";
    }

    if (dnsDomainIs(host, ".stillwell.me") ||
        dnsDomainIs(host, ".fortawesome.org")) {
        return "SOCKS5 localhost:9080; DIRECT;";
    }

    if (dnsDomainIs(host, ".onion") ||
        dnsDomainIs(host, ".i2p")) {
        return "SOCKS5 localhost:9050";
    }

    return "DIRECT;";

}
~~~

So, I have three different SOCKS5 proxies running: tor listening on localhost
port 9050, and ssh proxies on ports 9060, 9070, and 9080 that connect
respectively to Grid'5000, my work intranet, and my personal web server. The
normal way to set up a SSH proxy is to use a command like this:

    ssh -D 9080 squid.stillwell.me

Of course, if the ssh connection dies the proxy will also stop working, so
a standard approach is to use something like autossh to monitor the connection
and keep the proxy working:

    autossh -D 9080 squid.stillwell.me

Personally, I don't like to have the ssh connections up and running unless I'm
using them, so I came up with a different solution mini-inetd:

    mini-inetd 127.0.0.1:9080 /home/mark/.local/bin/inetd-ssh-proxy inetd-ssh-proxy squid 9081

The mini-inetd command will listen on the specified port (localhost, 9080) and,
when something tries to connect it will run the inetd-ssh-proxy command with
the argument vector ["inetd-ssh-proxy", "squid", "9081"] -- confusingly, it
will also override the zeroth argument, which is usually just the full path to
the command, so this value needs to be specified twice. Like regular inetd,
mini-inetd will expect the started service to interact through stdin/stdout
rather than through a network socket.

~~~ {.bash}
#!/bin/bash
# inetd-ssh-proxy
HOST=$1
PORT=$2

if ! nc -zw1 localhost ${PORT}; then 
    if ssh -O check ${HOST} 2>/dev/null; then
        ssh -O forward -D ${PORT} ${HOST}
    else
        ssh -Nn -o BatchMode=yes -o ExitOnForwardFailure=yes -D ${PORT} ${HOST} >/dev/null 2>&1
    fi
fi

nc -w3 localhost ${PORT}
~~~

This script first checks the specified port where the "real" ssh proxy should
listen using netcat. If the proxy isn't currently running, it will check if
there is an active ControlMaster connection that it can add a port mapping to,
otherwise it will start a new ssh proxy connection. Finally, the script uses
netcat to connect the ssh proxy to stdin and stdout. The final result is that
the proxy to the remote system always appears to be available, but the ssh
connection is only started when I try to connect to a website that needs it.

