*********************************
BASIC compile of internal modules
*********************************

Programs that live in GPL.BP are for the most part "internal" programs. As such, they need to be compiled in a special way.
You must start Scarlet with the internal flag:

/usr/qmsys/bin/qm -internal

You should then be able to compile programs that use the $internal compiler directive as well as the extended version of the $catalogue directive. 

To create a Op-code listing of your program, you add the $EXPLIST compiler directive to your program source. Use of this directive also requires starting Scarlet  with -internal flag.

Note: compiler directive names are hardcoded in the compiler source (BCOMP), not all of which are defined in the documentation, see internal subroutine proc.directive.

Example::

 sudo sd -INTERNAL

 PROGRAM HELLO
 $EXPLIST
 PRINT "Hello SDUSER, What is your name?: "
 INPUT UNAME
 PRINT UNAME: " Have a great day!" 
 END


 BASIC BP HELLO

HELLO.LIS::

         0000A5:       LD0
         0000A6:       LDSTR    "Hello SDUSER, What is your name?: "
         0000CA:       PRNL
      4  INPUT UNAME
         0000CB:       LDSLCL   UNAME
         0000CD:       LD0
      5  PRINT UNAME: " Have a great day!"
         0000CE:       LD0
         0000CF:       INPUT
         0000D1:       LD0
         0000D2:       LDSLCL   UNAME
         0000D4:       LDSTR    " Have a great day!"
         0000E8:       CAT
         0000E9:       PRNL
      6  END
         0000EA:       RETURN

INTERNAL Compiler Directives
============================

BCOMP compiler directives hardcoded into BCOMP routine PROC.DIRECTIVE:

EXPLIST - create OP Code Listing 

FLAGS - ALLOW.BREAK   sets object header flag hdr.allow.break

   CPROC           sets object header flag hdr.is.cproc
   
   DEBUGGER        sets object header flag hdr.is.debugger
   
   NETFILES        sets object header flag hdr.netfiles
   
   TRUSTED         sets object header flag hdr.is.trusted
   
INTERNAL - sets object header flag hdr.internal

RECURSIVE - sets object header flag hdr.recursive, used to identify programs that will be included in the pcode file.
  

