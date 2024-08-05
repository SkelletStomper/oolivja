# OOLIVJA - Object-Oriented Lightweight Intelligently-Verifying JSON Accessor

## Introduction

OOLIVJA aims to offer a better way of saving and interacting with JSON-files. The tables and entries of traditional DBMS are called Classes and Objects.

The Tables, called Classes, have to be pre-defined before writing entries, so-called objects, into it. These objects are read and written as JSON-Data, although methods are in place to access and alter individual parameters of these objects. Fixed-Size Variables, such as ints, bools or strings/lists with a special setting, can be changed immediately. 

For changing dynamic-length strings and lists, the changes have to be committed, on which the database gets restructued to contract/expand. This is expensive - but OOLIVJA is mainly intended as a read-only database for storing JSON Configurations for video games. Therefore, changing strings or other lists often is not the intended use.

### Goals
OOLIVJA wants to be light-weight - both in features and in file size. 
That means that its main focus is minimal size of the database on the hard drive and a reduced core functionality. For that purpose, various steps are taken to make every bit count, and have as much data bits to controlling bits as possible. 

### Non-Goals
OOLIVJA does not aim to offer multi-user access. Reading access is fine, but no garantuess are made for Writing Access. 
It also doesn't offer the full freedom that JSON normally offers, with users having to define the stored data pretty strictly.

Additionally, it does not offer no-loss on a crash or invalid transaction. While there is an option to keep a backup copy in-memory during runtime, transactions that have been made since the start of the program might get lost.

There is no efforts taken to optimize analytics. The basic Querying is fast, but there are no structures in the database to support things like filtering queries, etc. There is a distribution without this functionality what-soever.


## Database Structure

The Database consists of three main parts. At the beginning is the header, detailing various settings, the version number, etc. 

Second is the Class Definition Registry, where Classes get defined with all the Data needed to correctly parse the individual objects.

Lastly is the Data Heap, where all data is stored.


### General Header

The Database header contains the following Information:

Version Number
CDR Offset
DH Offset


### Class Definition Registry (CDR)
The Class Definition Registry consists of a header containing the Lenght of the DDR, and a varying amount of Class headers. Each of these Class headers describes a class of which objects can be saved, and is integral to correctly reading the objects on the Data Heap.

### Class Headers
The Class Header saves the name of the class, which must be unique, and whether or not the class is fixed-size. Fixed-Size means that the class only contains fixed-size parameters, such as bools, ints or fixed-size strings, or fixed-sized lists of these types. Then, they save a dict with all the necessary information to correctly parse the class.

### Data Types
There are 6 different Data types that classes can store. Two of them can be compounded further with these types. The 3-bit number next to their name marks their ID in the Variable Header. Each variable has a name that must be <=32 ascii chars long.

#### **int - 000**
Saves an integer. Settings:

length: specifies the length of the int in bits from 1 to 256

sign: whether or not the int is signed.

#### **float - 001**
Saves a floating point variable. Settings:

precision: can be either float (32 bits) or double (64 bits).

#### **bool - 010**
Saves a boolean Variable. No special Settings.

#### **string - 011**
Saves a string. Settings:

length: maximum length in chars from 1 to 65.536.

fixed: when true, database will pad strings shorter than the maximum length. Allows for changing of the string without reformating the databse.  

#### **dict - 101**
Saves a dictionary which can save other variables, much like the class itself. Settings:

nullable: when true, value can be empty with NULL.

Sub-Variables can be specified as desired.

#### **list - 111**
Saves a dynamic amount of a specified variable type. Settings:

Saved type: type that is saved in the list
length: Max list length
fixed: When true, lists shorter than the maximum will be padded with with default entries. Only works with fixed sub-variables.




### Data Heap (DH)
The Data heap starts with the Data Heap Header (DHH). The DHH, for every class, specifies the Offset from the start of the DH to the start of the Object Header (OH) for the class.

The Object header saves the the offset from itself to the next Object header, as well as the offset to each 
