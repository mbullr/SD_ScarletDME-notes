************************
Python in SD-dev-ext
************************

   
Embedding Python directly in the SD application.
========================================================

SD-dev-ext uses the python c/api https://docs.python.org/3/c-api/index.html to embed python in the SD application.
Basic function SDME.EXT is used to pass / access data from the python interpreter and execute c/api functions.

The following programs found in GPL.BP provide the BASIC interface to the python interpreter.

* PY_INITIALIZE  calls api function Py_Initialize() - start the python interpreter
* PY_FINALIZE    calls api function Py_FinalizeEx() - stop the python interpreter
* PY_RUNSTRING   calls api function PyRun_String() - run a python script from string
* PY_RUNFILE     calls api function PyRun_File() - run a python script from file
* PY_GETATTR     calls api function PyMapping_GetItemString() - access the value of a python object.
* remember strings are UTF-8 in python and must be converted to bytes (decode in encode out) good reference here: https://nedbatchelder.com/text/unipain.html 

To build / install  SD-dev-ext with an embedded Python interpreter (Ubuntu 24.04):

Note: Makefile_py looks for the python header file in /usr/include/python3.12. If you have not done so, you will need to install python3.12-dev::

    $ sudo apt install python3.12-dev


    Changes / additions to main branch:
	
       GPL.BP/BCOMP 
       GPL.BP/OPCODES.H	   
       GPL.BP/PY_INITIALIZE
       GPL.BP/PY_FINALIZE  
       GPL.BP/PY_RUNSTRING 
       GPL.BP/PY_RUNFILE   
       GPL.BP/PY_GETATTR

       BP/PY_TEST
       BP/PY_TEST_RUN_FILE
	   
       TEST/myscript.py
	   
Compile then run the python test program BP PY_TEST

You should see::

    RUN BP PY_TEST
    embedded python test, hit <enter> to start?
    ==================================================
    attempt init
    status = 0
    ==================================================
    script text:
    FMRK   = chr(254)
    VMRKM  = chr(253)
    SVMRK  = chr(252)
    result = ""
    countdown = {"3" : "Three", "2" : "Two", "1" : "One", "0" : "Ignition!"}
    for count in countdown:
      result += count + " " + countdown[count] + FMRK 
    result = bytes(result, 'latin')
    ==================================================
    attempt to run script:
    status = 0
    ==================================================
    attempt to access python object: result
    result:
    3 Three�2 Two�1 One�0 Ignition!�
    3 Three
    2 Two
    1 One
    0 Ignition!

    status = 0
    ==================================================
    attempt to access python object: countdown
    countdown:
    {'3': 'Three', '2': 'Two', '1': 'One', '0': 'Ignition!'}
    status = 0
    ==================================================
    attempt to access nonexistent python object: dummy
    dummy:
    Fetch of dict key dummy failed.
    status = 1
    ==================================================
    attempt finalize
    KeyError: 'dummy'
    status =0

