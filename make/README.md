
[HOME](../README.md)

# Table of Contents
* [Build system](#Build-system)

# Basic idea
Create binary in different directory from source dir, each Makefile will first include "make.incl", and at the end include "make.incl2"

# Detail of global "make.incl" and "make.incl2"
## "make.incl" : 
1. setting root and related variables:
```
ROOT := $(strip $(patsubst %/make.incl, %,$(word $(words $(MAKEFILE_LIST)), $(MAKEFILE_LIST))))
OBJROOT := $(ROOT)/object_root
BIN_DIR := ${OBJROOT}/bin
LIB_DIR := ${OBJROOT}/lib
```
2. set compile and shell related commands:
```
CC := /depot/qsc/QSCO/bin/gcc
CXX := /depot/qsc/QSCO/bin/g++
AR := /bin/ar
ARFLAGS := -cvr
MKDIR := mkdir -p
MV := mv -f
RM := rm -f
SED := sed
TEST := test
```
3. set QT related variables
```
QT_LIBS := -lQtNetwork -lQtGui -lQtCore -lX11 -lSM -lICE -lfontconfig \
        -lfreetype -lXext -lXrender -lpng -ljpeg -lmng -ltiff
QT_ROOT := /global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized
QT_PATH := $(QT_ROOT)/bin/
QT_MOCSOURCE = $(addprefix moc_, $(addsuffix .cpp, $(basename $(QT_MOCFILE))))
QT_RCCSOURCE = $(addprefix qrc_, $(addsuffix .cpp, $(basename $(QT_RCCFILE))))
QT_UICSOURCE = $(addprefix ui_, $(addsuffix .h, $(basename $(QT_UICFILE))))
```
4. set OBJECT_FILE and DEP_FILES(recursive variables) for each
   Makefile to use:
```
OBJECT_FILE = $(addprefix $(OBJECTDIR)/, $(addsuffix .o, $(basename $(SOURCE_FILE))))
DEP_FILES = $(addprefix $(OBJECTDIR)/, $(addsuffix .dep, $(basename $(SOURCE_FILE))))
```
5. set global compile related flags
```
CFLAGS := -I. -I$(ROOT)/lmserver/src/include -I$(QT_ROOT)/include/QtCore \
-I$(QT_ROOT)/include/QtNetwork -I$(QT_ROOT)/include/QtGui 
CXXFLAGS := -I. -I$(ROOT)/lmserver/src/include -I$(QT_ROOT)/include/QtCore \
-I$(QT_ROOT)/include/QtNetwork -I$(QT_ROOT)/include/QtGui 
LDFLAGS :=  -static-libgcc /depot/qsc/QSCO/GCC/lib64/libstdc++.a -static-libstdc++ \
-L$(LIB_DIR) -L/global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized/lib
LIBS := $(QT_LIBS) -pthread -ldl -lz
```

## "make.incl2" :
1. define create_dirs and clean target
```
create_dirs:
	$(TEST) -d $(BIN_DIR) || $(MKDIR) $(BIN_DIR)
	$(TEST) -d $(LIB_DIR) || $(MKDIR) $(LIB_DIR)
	$(TEST) -d $(OBJECTDIR) || $(MKDIR) $(OBJECTDIR)
clean:
	$(RM) $(OBJECT_FILE) $(TARGET) $(QT_MOCSOURCE) $(QT_RCCSOURCE) $(QT_UICSOURCE)
```
2. define file rules:
```
$(OBJECTDIR)/%.o: %.cpp
	$(CXX) -c $< -o $@ $(CXXFLAGS) 
moc_%.cpp: %.h 
	$(QT_PATH)/moc $(DEFINES) $(INCLUDE) $< -o $@
qrc_%.cpp: %.qrc
	$(QT_PATH)/rcc $< -o $@
ui_%.h: %.ui
	$(QT_PATH)/uic $< -o $@
$(OBJECTDIR)/%.dep: %.cpp
	@echo generate $@
	$(CXX) -M $(CXXFLAGS) $< > $@.$$$$; \
	sed 's,\($*\).o[ :]*,$(OBJECTDIR)/\1.o $@ : ,g' <  $@.$$$$ > $@; \
	$(RM) $@.$$$$
```
 
# local Makfile
1. Use a shell script to get the "make.incl" file and include it.
```
INCL_FILE := $(shell path="."; \
               ret=""; \
              while [[ $$path != / ]]; \
              do \
                ret="$$(find $$path -maxdepth 1 -mindepth 1 -iname "make.incl")"; \
                if [ x"$$ret" != x"" ]; then \
                  break; \
                fi; \
                path="$$(readlink -f "$$path"/..)"; \
              done; \
              echo $$ret)
include $(INCL_FILE)
```
2. define related binary directory variable
```
OBJECTDIR:=$(patsubst $(ROOT)/%, $(OBJROOT)/%, $(CURDIR))
```
3. Define the QT use files variables:
```
QT_MOCFILE = TasksManager.h MainWindow.h
QT_RCCFILE =
QT_UICFILE = mainwindow.ui 	 
```
4. Define Target, source files and local compile related flags.
```
TARGET = $(BIN_DIR)/customfault
SOURCE_FILE = main.cpp LicManager.cpp TasksManager.cpp MainWindow.cpp \
$(QT_MOCSOURCE) $(QT_RCCSOURCE)
LOCAL_LIBS = -lscl -lutils
```
5. include dependency files
```
include $(DEP_FILES)
```
6. define rules:
```
all: create_dirs $(TARGET)
$(TARGET): $(QT_UICSOURCE) $(OBJECT_FILE) 
     $(CXX) -o $@ $(OBJECT_FILE) $(LDFLAGS) $(LOCAL_LIBS) $(LIBS) 
```
7. include "make.incl2"
```
include $(ROOT)/make.incl2
```

# sample files

make.incl
```
# -*-Makefile-*-
# setting ROOT and OBJROOT
#
ROOT := $(strip $(patsubst %/make.incl, %,$(word $(words $(MAKEFILE_LIST)), $(MAKEFILE_LIST))))
OBJROOT := $(ROOT)/object_root

#
#  set compiler and related shell commands
#
CC := /depot/qsc/QSCO/bin/gcc
CXX := /depot/qsc/QSCO/bin/g++
AR := /bin/ar
ARFLAGS := -cvr
MKDIR := mkdir -p
MV := mv -f
RM := rm -f
SED := sed
TEST := test

#
# set bin and lib output directories
#
BIN_DIR := ${OBJROOT}/bin
LIB_DIR := ${OBJROOT}/lib

#
# Qt special function
#
QT_LIBS := -lQtNetwork -lQtGui -lQtCore -lX11 -lSM -lICE -lfontconfig \
        -lfreetype -lXext -lXrender -lpng -ljpeg -lmng -ltiff
QT_ROOT := /global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized
QT_PATH := $(QT_ROOT)/bin/
QT_MOCSOURCE = $(addprefix moc_, $(addsuffix .cpp, $(basename $(QT_MOCFILE))))
QT_RCCSOURCE = $(addprefix qrc_, $(addsuffix .cpp, $(basename $(QT_RCCFILE))))
QT_UICSOURCE = $(addprefix ui_, $(addsuffix .h, $(basename $(QT_UICFILE))))
OBJECT_FILE = $(addprefix $(OBJECTDIR)/, $(addsuffix .o, $(basename $(SOURCE_FILE))))
DEP_FILES = $(addprefix $(OBJECTDIR)/, $(addsuffix .dep, $(basename $(SOURCE_FILE))))

#
# global compile related flags
#
CFLAGS := -I. -I../include -I$(QT_ROOT)/include/QtCore -I$(QT_ROOT)/include/QtNetwork -I$(QT_ROOT)/include/QtGui 
CXXFLAGS := -I. -I../include -I$(QT_ROOT)/include/QtCore -I$(QT_ROOT)/include/QtNetwork -I$(QT_ROOT)/include/QtGui 
LDFLAGS :=  -static-libgcc /depot/qsc/QSCO/GCC/lib64/libstdc++.a -static-libstdc++ -L$(LIB_DIR) -L/global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized/lib
LIBS := $(QT_LIBS) -pthread -ldl -lz
```
"make.incl2"
```
# -*-Makefile-*-
#  make patterns, needed to include after all variables are setting well
#
create_dirs:
	$(TEST) -d $(BIN_DIR) || $(MKDIR) $(BIN_DIR)
	$(TEST) -d $(LIB_DIR) || $(MKDIR) $(LIB_DIR)
	$(TEST) -d $(OBJECTDIR) || $(MKDIR) $(OBJECTDIR)

clean:
	$(RM) $(OBJECT_FILE) $(TARGET) $(QT_MOCSOURCE) $(QT_RCCSOURCE) $(QT_UICSOURCE)

$(OBJECTDIR)/%.o: %.c
	$(CC) -c $< -o $@ $(CFLAGS) 

$(OBJECTDIR)/%.o: %.cpp
	$(CXX) -c $< -o $@ $(CXXFLAGS) 

moc_%.cpp: %.h 
	$(QT_PATH)/moc $(DEFINES) $(INCLUDE) $< -o $@

qrc_%.cpp: %.qrc
	$(QT_PATH)/rcc $< -o $@

ui_%.h: %.ui
	$(QT_PATH)/uic $< -o $@

$(OBJECTDIR)/%.dep: %.cpp
	@echo generate $@
	$(CXX) -M $(CXXFLAGS) $< > $@.$$$$; \
	sed 's,\($*\).o[ :]*,$(OBJECTDIR)/\1.o $@ : ,g' <  $@.$$$$ > $@; \
	$(RM) $@.$$$$
```
Root Makefile
```
SUBDIRS := utils IsAmd SclApis LmServer CustomSim CustomFault

.PHONY: all clean $(SUBDIRS)

all clean: $(SUBDIRS)

$(SUBDIRS):
	$(MAKE) -C $@ $(MAKECMDGOALS)

```
Subdir Makefile
```
INCL_FILE := $(shell path="."; \
               ret=""; \
              while [[ $$path != / ]]; \
              do \
                ret="$$(find $$path -maxdepth 1 -mindepth 1 -iname "make.incl")"; \
                if [ x"$$ret" != x"" ]; then \
                  break; \
                fi; \
                path="$$(readlink -f "$$path"/..)"; \
              done; \
              echo $$ret)

include $(INCL_FILE)

OBJECTDIR:=$(patsubst $(ROOT)/%, $(OBJROOT)/%, $(CURDIR))

QT_MOCFILE = TasksManager.h MainWindow.h
QT_RCCFILE =
QT_UICFILE = mainwindow.ui 

TARGET = $(BIN_DIR)/customfault
SOURCE_FILE = main.cpp LicManager.cpp TasksManager.cpp MainWindow.cpp $(QT_MOCSOURCE) $(QT_RCCSOURCE)
LOCAL_LIBS = -lscl -lutils

include $(DEP_FILES)

all: create_dirs $(TARGET)

$(TARGET): $(QT_UICSOURCE) $(OBJECT_FILE) 
	$(CXX) -o $@ $(OBJECT_FILE) $(LDFLAGS) $(LOCAL_LIBS) $(LIBS) 

include $(ROOT)/make.incl2

```
