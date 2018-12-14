
[HOME](../README.md)

# Table of Contents
* [Build system](#Build-system)

# Build System

# Include files for all Makefiles
We can create two files in the root directory for all Makefile to use, 
1. make.incl in the begining of Makefile, it looks like:
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
2. "make.incl2" at the end of Makefile, setting pattern
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
# Root Makefile, recusive make
```
SUBDIRS := utils IsAmd SclApis LmServer CustomSim CustomFault

.PHONY: all clean $(SUBDIRS)

all clean: $(SUBDIRS)

$(SUBDIRS):
	$(MAKE) -C $@ $(MAKECMDGOALS)

```
# Subdir Makefile
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
