**********************************
Adding Op Codes / Functions to SD
**********************************

This is a summation of the steps necessary to add a new function to the SD BASIC compiler (GPL.BP/BCOMP) and the associated op code to process the function in the Virtual Machine. 
In this example we add the BASIC Function SDME.EXT and the corresponding op code function op_sdme_ext to SD.

Adding The Op Code
===================

Adding new op code to SD requires:

 1) Create the C function code and store in the gplsrc directory.
 
    For this example, refer to gplsrc/op_sdme_ext.c 
 
 2) Add the function file name op_sdme_ext to file gpl.src. This file is used by the make process to define the c sources to compile.
	
 3) Add the op code to gplsrc/opcodes.h. This include file is used to build the dispatch array used by the Virtual Machine to call the c function that processes the associated op code::
    
            	Code    Keyword      Opcode        Function      Format            Stack 
        _opc_(0xCFFF, OP_SDMEEXT,  "SDMEEXT",    op_sdme_ext,  OPCODE_BYTE,        -2)

     
 
 4) Make sure we can compile SD with make clean, then make. 
 
 5) Update OPCODES.H in sdsys/GPL.BP using program OPGEN. In this example OPGEN will add the following entry in sdsys/GPL.BP/OPCODES.H::
       
	   $define OP.SDMEEXT      53247  ;* CFFF

    OPGEN has converted the text OP_SDMEEXT in gplsrc/opcodes.h to text OP.SDMEEXT in sdsys/GPL.BP/OPCODES.H and assigned the value 53247  (0xCFFF) to the symbolic name.
	   
    The Opcode text "SDMEEXT" is entered into the multi value string prefixed.opcodes defined in sdsys/GPL.BP/OPCODES.H and used by BCOMP when the $EXPLIST compiler directive is specified.

    I believe the "Stack" value represents how many times the execution stack (estack) needs to be "popped" to get back to the descriptor for the return value for the function.	I have not seen where this value used in the Virtual Machine code, I suspect it is for some sort of debugging tool not supplied when Ladybridge released the
    open source version.  But this is only a guess!	   

    BIG NOTE If you copied opcodes.h over from a windows system to linux make sure you convert the file to linux style
    or OPGEN will fail! Use something like dos2unix opcodes.h
	
 6) Add the corresponding function to BASIC - next section

Adding The function to BASIC (BCOMP)
====================================

WARNING !!! Before you start editing file BCOMP, make a backup copy of the current bcomp bytecode object in folder gcat (/sdsys/gcat/$BCOMP). You need a working copy of the compiler to recompile BCOMP
(kind of a chicken and the egg thing).  If you make a mistake when editing BCOMP, and BCOMP fails to compile, you are stuck! This is because as part of the BASIC compile process,
the current copy of the bytecode object is deleted.  If you make a copy of $BCOMP in gcat, you can simply copy it back and you are back in business.
YOU HAVE BEEN WARNED

 We can add Statements, Internal (restricted) Statements, Intrinsic functions  and Internal (restricted) Intrinsic functions


 Adding a new statement requires an entry in the STATEMENTS list and a
 corresponding entry in the ON GOSUB that uses this list.
 
 Adding a new internal statement requires an entry in the RESTRICTED.STATEMENTS list and a
 corresponding entry in the ON GOSUB that uses this list.

 Adding a new intrinsic function requires entries in the INTRINSICS and
 INTRINSIC.OPCODES lists and a corresponding entry in that ON GOSUB.
 
 Adding a new internal intrinsic function requires entries in the INT.INTRINSICS and
 INT.INTRINSIC.OPCODES lists and a corresponding entry in that ON GOSUB.
 
 SDME.EXT will be a function to call other c code functions.  Because of this we should at least make this an internal function, so we add the following lines to BCOMP::
    
   Addition of the new function name and op code to multi value strings int.intrinsics and int.intrinsic.opcodes. 
   
   int.intrinsics<-1> = "SCAN"            ; int.intrinsic.opcodes<-1> = OP.BTSCAN
   int.intrinsics<-1> = "SDME.EXT"        ; int.intrinsic.opcodes<-1> = OP.SDMEEXT     <== our new function name and op code symbolic name
   int.intrinsics<-1> = "SORTDATA"        ; int.intrinsic.opcodes<-1> = OP.SORTDATA
   
   Adding to ON GOSUB for intrinsic function (a little past label "emit.var.reference.common: in BCOMP")
   
   locate x.symbol.name in int.intrinsics<1> by 'AL' setting i then
                     gosub get.token           ; * Skip left bracket
                     intrinsic.stack = int.intrinsic.opcodes<i> : @fm : intrinsic.stack 
                     on i goto in.none,       ;* ABORT.CAUSE
                               in.two,        ;* AKMAP
                               in.one,        ;* ANALYSE
                               in.none,       ;* BREAK.COUNT
                               in.btree,      ;* BTREE
                               in.one,        ;* CHANGED
                                 .                 .
                                 .                 .
                                 .                 .
                               in.scan,       ;* SCAN
                               in.three,      ;* SDME.EXT      <== emit byte code for our new function 
                               in.none,       ;* SORTDATA								 
   


The last thing we need to do is re compile BCOMP. Login into SD (you may need root access) with:
 
sudo sd -INTERNAL

Then compile:

BASIC GPL.BP BCOMP

If all is correct, function SDME.EXT is now available for use.
