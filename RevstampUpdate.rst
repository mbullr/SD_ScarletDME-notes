*********************************
Revision Stamp Updating
*********************************

1) Shutdown the db application (sd or qm -stop)

2) Change /gplsrc/revstamp.h  to correct rev values.

3) make  ( recompile vm from gplsrc )

4) Start the db application (sd or qm -start)

5) Login to  sd with sudo sd -asdsys -internal or sudo qm -aqmsys -internal depending on system.

   You will get the  "Revision level cross-check error",  but land at TCL, (without -internal the connection terminates).
	
6) BASIC GPL.BP CPROC LOGIN BASIC BCOMP PTERM CATALOG 
   
   In CPROC we have the following lines which during compile, recompile program REVSTAMP and then execute it, updating GPL.BP/REVSTAMP.H then finally including the updated REVSTAMP.H file::   
        
        Line  115: $execute 'BASIC GPL.BP REVSTAMP'
        Line  116: $execute 'RUN GPL.BP REVSTAMP'
        Line  117: $include revstamp.h
    
   GPL.BP/REVSTAMP.H is updated to what is in /gplsrc/revstamp.h
   
7) Logout and back in as in step 5, you will get the message to update VOC, but it will not work, more below

8) BASIC GPL.BP *

   Compile all remaining files that depend on GPL.BP/REVSTAMP.H

9) Fix VOC Update message

   On Login You See::

        This program is free software and is supplied with ABSOLUTELY NO WARRANTY.
        You are welcome to modify or redistribute this software subject to certain
        conditions.  For details type CONFIG GPL.

        Your VOC is at release level 2.6-6 
   
        Update VOC to new release?


   Which never updates correctly because $RELEASE record in NEWVOC has::

        X Release level
        2.6-6

   In order to fix this we must edit the $RELEASE record in NEWVOC:: 

        X Release level
        x.y-z    where x.y-z matched what we entered in /gplsrc/revstamp.h

   Now Update VOC to new release?  Y - works. 