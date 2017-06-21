# dellabetta/tcpserver
Alpine-based image with just tcpserver by D. J. Bernstein (https://cr.yp.to/ucspi-tcp/tcpserver.html)
Inspired by https://github.com/appropriate/docker-nc
Source code by D. J. Bernstein (https://cr.yp.to/ucspi-tcp/tcpserver.html)
Patch by 

## Usage manual

```console
Usage:
$ docker run -it --rm dellabetta/tcpserver opts host port prog

opts is a series of getopt-style options. host is one argument. port is one argument. prog consists of one or more arguments.
tcpserver waits for connections from TCP clients. For each connection, it runs prog, with descriptor 0 reading from the network and descriptor 1 writing to the network. It also sets up several environment variables.

The server's address is given by host and port. port may be a name from /etc/services or a number; if it is 0, tcpserver will choose a free TCP port. host may be 0, allowing connections to any local IP address; or a dotted-decimal IP address, allowing connections only to that address; or a host name, allowing connections to the first IP address for that host. Host names are fed through qualification using dns_ip4_qualify.

tcpserver exits when it receives SIGTERM.

Options:

General options:
    -q: Quiet. Do not print error messages.
    -Q: (Default.) Print error messages.
    -v: Verbose. Print error messages and status messages.
Connection options:
    -c n: Do not handle more than n simultaneous connections. If there are n simultaneous copies of prog running, defer acceptance of a new connection until one copy finishes. n must be a positive integer. Default: 40.
    -x cdb: Follow the rules compiled into cdb by tcprules. These rules may specify setting environment variables or rejecting connections from bad sources. You can rerun tcprules to change the rules while tcpserver is running.
    -X: With -x cdb, allow connections even if cdb does not exist. Normally tcpserver will drop the connection if cdb does not exist.
    -B banner: Write banner to the network immediately after each connection is made. tcpserver writes banner before looking up $TCPREMOTEHOST, before looking up $TCPREMOTEINFO, and before checking cdb. This feature can be used to reduce latency in protocols where the client waits for a greeting from the server.
    -g gid: Switch group ID to gid after preparing to receive connections. gid must be a positive integer.
    -u uid: Switch user ID to uid after preparing to receive connections. uid must be a positive integer.
    -U: Same as -g $GID -u $UID. Typically $GID and $UID are set by envuidgid.
    -1: After preparing to receive connections, print the local port number to standard output.
    -b n: Allow a backlog of approximately n TCP SYNs. On some systems, n is silently limited to 5. On systems supporting SYN cookies, the backlog is irrelevant.
    -o: Leave IP options alone. If the client is sending packets along an IP source route, send packets back along the same route.
    -O: (Default.) Kill IP options. A client can still use source routing to connect and to send data, but packets will be sent back along the default route.
    -d: Delay sending data for a fraction of a second whenever the remote host is responding slowly. This is currently the default, but it may not be in the future; if you want it, set it explicitly.
    -D: Never delay sending data; enable TCP_NODELAY.
    Data-gathering options:
    -h: (Default.) Look up the remote host name in DNS to set the environment variable $TCPREMOTEHOST.
    -H: Do not look up the remote host name in DNS; remove the environment variable $TCPREMOTEHOST. To avoid loops, you must use this option for servers on TCP port 53.
    -p: Paranoid. After looking up the remote host name in DNS, look up the IP addresses in DNS for that host name, and remove the environment variable $TCPREMOTEHOST if none of the addresses match the client's IP address.
    -P: (Default.) Not paranoid.
    -l localname: Do not look up the local host name in DNS; use localname for the environment variable $TCPLOCALHOST. A common choice for localname is 0. To avoid loops, you must use this option for servers on TCP port 53.
    -r: (Default.) Attempt to obtain $TCPREMOTEINFO from the remote host.
    -R: Do not attempt to obtain $TCPREMOTEINFO from the remote host. To avoid loops, you must use this option for servers on TCP ports 53 and 113.
    -t n: Give up on the $TCPREMOTEINFO connection attempt after n seconds. Default: 26.
    
Examples:

$ docker run -it -p 5678:5678 --rm dellabetta/tcpserver 0 5678 echo 5678
```

## License

Copyright © 2017 Filippo Della Betta

All contents licensed under the [MIT License](LICENSE)
