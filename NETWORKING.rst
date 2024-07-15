**********************************************
Network Connections, stdin, stdout and  xinetd
**********************************************

Or "How the heck is SD communicating over the network"

`From https://www.thegeekdiary.com/understanding-etc-xinetd-d-directory-under-linux/ <https://www.thegeekdiary.com/understanding-etc-xinetd-d-directory-under-linux/>`__

"The xinetd daemon is a TCP wrapped super service which controls access to a subset of popular network services including FTP, IMAP, and telnet.
It also provides service-specific configuration options for access control, enhanced logging, binding, redirection, and resource utilization control.

When a client host attempts to connect to a network service controlled by xinetd ,
the super service receives the request and checks for any TCP wrappers access control rules.
If access is allowed, xinetd verifies that the connection is allowed under its own access rules for that service
and that the service is not consuming more than its allotted amount of resources or in breach of any defined rules.
It then starts an instance of the requested service and passes control of the connection to it.
Once the connection is established, xinetd does not interfere further with communication between the client host and the server."

xinetd takes the socket descriptor associated with TCP port X and maps it to the service's standard input stdin (fd "0")
and service's standard output stdout (fd "1").

In other words, assuming a custom TCP service written in C, data coming into port X is mapped to stdin of the service
and stdout of the service is mapped to data coming out of port Y. 

xinetd services read stdin and write to stdout, letting xinetd handle the gory details of TCP/IP, rather than keeping track of their own sockets

