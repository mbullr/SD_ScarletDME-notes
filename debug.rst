*****************************
DEBUG configuration parameter
*****************************

16 bits from from DEBUG configuration parameter::

    #define SSF_PRTDEBUG   0x00010000      dec  1  Debug printer actions 
    #define SSF_SDFIX      0x00020000      dec  2  Allow SDFix in interactive mode 
    #define SSF_MONITOR    0x00040000      dec  4  Run in monitor mode 
    #define SSF_INT_PDUMP  0x00080000      dec  8  Allow PDUMP to dump internal mode 
    #define SSF_NO_FILE_CLEANUP 0x00100000 dec 16  Inhibit cleanup of file table 
    #define SSF_APISRVR_LOG 0x00200000     dec 32  APISRVR logs traffic 