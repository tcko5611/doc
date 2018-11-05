[HOME](../README.md)

# Table of Contents
* [Build system](#Build-system)

# Build System

# Include file for all Makefiles
We can create a file "make.incl" in the root directory for all Makefile to use, 
it looks like
```
#
# Define compile binary
#
CC = /depot/qsc/QSCO/bin/gcc
CXX = /depot/qsc/QSCO/bin/g++

#==================================================
# Qt special function
#==================================================
QT_LIBS = -lQtNetwork -lQtCore
QT_ROOT = /global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized
QT_PATH = $(QT_ROOT)/bin/
QT_MOCSOURCE = $(addprefix moc_, $(addsuffix .cpp, $(basename $(QT_MOCFILE))))
QT_RCCSOURCE = $(addprefix qrc_, $(addsuffix .cpp, $(basename $(QT_RCCFILE))))
QT_UICSOURCE = $(addprefix ui_, $(addsuffix .h, $(basename $(QT_UICFILE))))
OBJECT_FILE = $(addsuffix .o, $(basename $(SOURCE_FILE)))

#
# Define glbal c and cxx flags
#
CFLAGS = -I. -I$(QT_ROOT)/include/QtCore -I$(QT_ROOT)/include/QtNetwork
CXXFLAGS = -I. -I$(QT_ROOT)/include/QtCore -I$(QT_ROOT)/include/QtNetwork
LDFLAGS =  -static-libgcc /depot/qsc/QSCO/GCC/lib64/libstdc++.a -static-libstdc++ -L/global/libs/qt_qsck_2015.06/4.8.5/linux64_optimized/lib $(QT_LIBS) -pthread -ldl -lz
#
# Define the library directory 
#
BIN_DIR = $(CURDIR)/bin
LIBRARY_DIR = $(CURDIR)/library

ifeq ($(DEBUG),1)
CFLAGS += -g
CXXFLAGS += -g
endif # DEBUG

%.o:%.c
	$(CC) -c $< -o $@ $(CFLAGS) 

%.o:%.cpp
	$(CXX) -c $< -o $@ $(CXXFLAGS) 

moc_%.cpp: %.h 
	$(QT_PATH)/moc $(DEFINES) $(INCLUDE) $< -o $@

qrc_%.cpp: %.qrc
	$(QT_PATH)rcc $< -o $@

ui_%.h: %.ui
	$(QT_PATH)uic $< -o $@
```
* Root Makefile
```
#
# for all Makefile to know the Root Dir
#
export ROOT_DIR = $(CURDIR)
SUBDIRS := client server LmServer SclApis
.PHONY: all $(SUBDIRS)

all clean: $(SUBDIRS)

$(SUBDIRS):
	@echo $(CURDIR)
	$(MAKE) -C $@ $(MAKECMDGOALS)
```
# Subdir Makefile
```
include $(ROOT_DIR)/make.incl
QT_MOCFILE = LmServer.h EchoThread.h
QT_RCCFILE =
QT_UICFILE = 

TARGET = LmServer
SOURCE_FILE = main.cpp LmServer.cpp EchoThread.cpp $(QT_MOCSOURCE) $(QT_RCCSOURCE)
#OBJECT_FILE = $(addsuffix .o, $(basename $(SOURCE_FILE)))
#QT_MOCSOURCE = $(addprefix moc_, $(addsuffix .cpp, $(basename $(QT_MOCFILE))))

all: $(TARGET)
        echo $(CURDIR)

$(TARGET): $(OBJECT_FILE) $(QT_UICSOURCE)
        $(CXX) -o $@ $(OBJECT_FILE) $(LDFLAGS)

clean:
        rm -f *.o LmServer
```
