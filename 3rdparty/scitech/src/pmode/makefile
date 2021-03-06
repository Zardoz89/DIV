#############################################################################
#
#                   Copyright (C) 1996 SciTech Software.
#                           All rights reserved.
#
# Descripton:   Generic makefile for the PMODE library. Builds the library
#               file and all example programs.
#
# $Date:   12 Feb 1996 21:58:56  $ $Author:   KendallB  $
#
#############################################################################

# Add DOS extender dependant flags to command line

CFLAGS          += $(DX_CFLAGS)
ASFLAGS         += $(DX_ASFLAGS)

# Name of library and object files required to build it

.IF $(STKCALL)
LIBFILE         = spmode$L
.ELSE
LIBFILE         = pmode$L
.ENDIF
LIBCLEAN        = pmpro*.lib pmpro*.dll
OBJECTS         = pmlite$O pmpro$O vflat$O _pmlite$O _pmpro$O _vflat$O

# Change destination for library file depending on extender being used

.IF $(USE_TNT)
LIB_DEST        := $(LIB_BASE)\tnt
.ELIF $(USE_DOS4GW)
LIB_DEST        := $(LIB_BASE)\dos4gw
.ELIF $(USE_X32)
LIB_DEST        := $(LIB_BASE)\x32
.ELIF $(USE_DPMI16)
LIB_DEST        := $(LIB_BASE)\dpmi16
.ELIF $(USE_DPMI32)
LIB_DEST        := $(LIB_BASE)\dpmi32
.ELIF $(USE_DOSX)
LIB_DEST        := $(LIB_BASE)\dosx
.ELIF $(USE_WIN16)
LIB_DEST        := $(LIB_BASE)\win16
.ELIF $(USE_WIN32)
LIB_DEST        := $(LIB_BASE)\win32
.END

# Names of all executable files built

EXEFILES        = memtest$E biosptr$E video$E isvesa$E callreal$E		\
                  mouse$E tick$E key$E key15$E brk$E altbrk$E           \
                  critical$E altcrit$E vftest$E

# Extra files for the professional version

.IMPORT .IGNORE: PRO

.IF $(BUILD_DLL)
all: $(LIBFILE)
.ELSE
all: $(EXEFILES)

$(EXEFILES): $(LIBFILE)

memtest$E:  memtest$O
biosptr$E:  biosptr$O
video$E:    video$O
isvesa$E:   isvesa$O
mouse$E:    mouse$O
tick$E:     tick$O
key$E:      key$O
key15$E:    key15$O
brk$E:    	brk$O
altbrk$E:   altbrk$O
critical$E: critical$O
altcrit$E:  altcrit$O
callreal$E: callreal$O
vftest$E: vftest$O
.ENDIF

# Define the list of object files to create dependency information for

DEPEND_OBJ      = $(OBJECTS) memtest$O biosptr$O video$O isvesa$O mouse$O   \
                  tick$O key$O key$O brk$O altbrk$O critical$O altcrit$O    \
                  callreal$O vftest$O

.INCLUDE: "$(SCITECH)\makedefs\common.mk"

