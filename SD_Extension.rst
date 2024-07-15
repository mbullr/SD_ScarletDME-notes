*********************************
SD Extend - SDME.EXT
*********************************

Generic function for extending SD / BASIC to interface with C code.

The purpose of this function is to add a generic function to SD / BASIC
to allow for the interface of C code and C functions.

Currently SD has 16 unassigned op codes for use by GPL developers

From opcodes.h::

  /* The last 16 extended opcodes will never be used by Ladybridge Systems.
   They may be used for private purposes by GPL developers but any such
   opcode subsequently incorporated into the later source will be
   moved to a new location.
  */
  _opc_(0xCFF2, OP_CFF0,     "OPCFF0",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF2, OP_CFF1,     "OPCFF1",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF2, OP_CFF2,     "OPCFF2",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF3, OP_CFF3,     "OPCFF3",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF4, OP_CFF4,     "OPCFF4",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF5, OP_CFF5,     "OPCFF5",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF6, OP_CFF6,     "OPCFF6",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF7, OP_CFF7,     "OPCFF7",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF8, OP_CFF8,     "OPCFF8",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFF9, OP_CFF9,     "OPCFF9",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFA, OP_CFFA,     "OPCFFA",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFB, OP_CFFB,     "OPCFFB",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFC, OP_CFFC,     "OPCFFC",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFD, OP_CFFD,     "OPCFFD",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFE, OP_CFFE,     "OPCFFE",     op_illegal2,  OPCODE_BYTE,         0)
  _opc_(0xCFFF, OP_CFFF,     "OPCFFF",     op_illegal2,  OPCODE_BYTE,         0)
  
As development advances, it is highly probable that the project will run out of "free" available op codes.

Addition of a generic way to interface with C / C Functions will reduce the need for additional op codes.

Function SDME.EXT
========================

 Function SDME.EXT(Arg, IsArgMV, Function_id)::

    Function_Id = the integer value used to identify what c code / function is execute.
    IsArgMV     = If passing a multiple arguments in Arg, set to true, otherwise false.  
    Arg         = string value passed to the c code / function. For functions requiring multiple arguments, set IsArgMV to true and pass arguments in a FIELD_MARK separated string, with a maximum of ?10? fields??

    Note: Plan is to use the BASIC function STATUS() to return function status:
	
	STATUS() = 0 successful call, or  STATUS() = 1 unsuccessful call
 

To view the steps taken to add this function see AddingOpcodes.rst


 

 