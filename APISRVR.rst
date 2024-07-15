********************
APIsrvr (was vbsrvr)
********************

SDCLIENT Command processor ../sdsys/GPL.BP/APISRVR

Started with -Q command line option, which sets is_sdApiSrvr = TRUE in kernel
In linuxio.c start_Connection change the default command processor to APISRVR via strcpy(command_processor, "$APISRVR") 

Login Process:
Network Login (API Connect())::

    vb.login
    if security enabled
       Extract username and password sent by client
       Call login(username,password) source (gplsrc/op_kernel.c) op_login 
                                      op_login calls (gplsrc/linuxio.c) login_user
                                      login_user validates username and password against /etc/shadow
                                        uses setgid / setuid to set group id / user id of process
       Note: login could possibly be replaced with getpeereid - get the effective credentials of a UNIX-domain peer, then use setgid / setuid to set group id / user id of process.
    if not valid linux user then
        reject this connection
    else
        if the user is not in the $LOGINS register reject this connection.   
      


Local login (API ConnectLocal())::

    vb.local.login:
    This connection is made via pipes. The API FORKS the process then uses execl (function replaces the currently running process with another process) to replace the child process with qm/sd process passing the pipes for Tx and Rx. Process will have group and user id of process using API.
    if security enabled
        if the user is not in the $LOGINS register reject this connection. 


Communicates with Client via READPKT and WRITEPKT  (both BCOMP internal mode functions)

op code for::

 READPKT    - OP.READPKT     /gplsc/op_tio.c    op_readpkt()  - Read QMClient data packet
                                                                           gplsrc\linuxio.c  read_socket
                                                                           gplsrc\linuxio.c  keyin 
                                                                           gplsrc\linuxio.c   do_input() ->  c std function read() from fd 0


 WRITEPKT   - OP.WRITEPKT    /gplsc/op_tio.c    op_writepkt()  -  Write QMClient data packet
                                                                           write_socket()  -  Write to socket / pipe
                                                                           to_outbuf()  -  Copy data to socket output buffer
                                                                           gplsrc\linuxio.c    flush_outbuf()  -  Flush socket output buffer
                                                                           gplsrc\linuxio.c    write_console  -> c std function write to fd 1



If one wanted to change the Client Server to JD3, would changing socket read / write to using READPKT / WRITEPKT do the trick?