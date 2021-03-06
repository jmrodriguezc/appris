# Makefile for mkdssp
#
#  Copyright Maarten L. Hekkelman, Radboud University 2008-2011.
# Distributed under the Boost Software License, Version 1.0.
#    (See accompanying file LICENSE_1_0.txt or copy at
#          http://www.boost.org/LICENSE_1_0.txt)
#
# This makefile includes a file called make.config. It will create a
# new one if it doesn't exist. In this make.config you can set site
# specific variables like the Boost library location.

firstTarget: mkdssp

include make.config

VERSION				= 2.0.4

DEST_DIR			?= /usr/local/
LIB_DIR				= $(BOOST_LIB_DIR)
INC_DIR				= $(BOOST_INC_DIR)
MAN_DIR				= $(DEST_DIR)man/man1/

BOOST_LIBS			= thread regex filesystem program_options date_time iostreams math_c99 system
LIBS				= $(BOOST_LIBS:%=boost_%$(BOOST_LIB_SUFFIX)) z bz2

DEFINES				= USE_COMPRESSION LINUX VERSION='"$(VERSION)"'
CC					= c++

CFLAGS				= $(INC_DIR:%=-I%) -iquote src -g -Wall -Wno-multichar -pthread
LDOPTS				= $(LIB_DIR:%=-L%) $(LIBS:%=-l%) -g -pthread

OBJ_DIR				= obj

ifeq ($(DEBUG),1)
OBJ_DIR				:= $(OBJ_DIR).dbg
CFLAGS				+= -g3
else
DEFINES				+= NDEBUG
CFLAGS				+= -O3
endif

CFLAGS				+= $(DEFINES:%=-D%)

DIST_NAME			= dssp-$(VERSION)

VPATH += src

OBJECTS = $(OBJ_DIR)/mkdssp.o $(OBJ_DIR)/dssp.o $(OBJ_DIR)/primitives-3d.o $(OBJ_DIR)/structure.o $(OBJ_DIR)/utils.o

mkdssp: $(OBJECTS)
	@ echo linking $@
	@ $(CC) -static -o $@ $^ $(LDOPTS)
	@ echo OK

include $(OBJECTS:%.o=%.d)

$(OBJECTS:.o=.d):

$(OBJ_DIR)/%.o: %.cpp
	@ if [ ! -d $(OBJ_DIR) ]; then mkdir $(OBJ_DIR); fi
	@ echo compiling $@
	@ $(CC) -MD -c -o $@ $< $(CFLAGS)

clean:
	rm -rf $(OBJ_DIR)/* mkdssp

install: mkdssp
	sudo install -m 755 mkdssp $(DEST_DIR)bin/mkdssp
	sudo install doc/mkdssp.1 $(MAN_DIR)mkdssp.1

dist: $(OBJECTS)
	@ rm -rf $(DIST_NAME)
	@ mkdir -p $(DIST_NAME)/src $(DIST_NAME)/doc
	@ echo "Copying files..."
	@ cp makefile.dssp $(DIST_NAME)/makefile
	@ cp changelog.dssp $(DIST_NAME)/changelog
	@ cp $(OBJECTS:$(OBJ_DIR)%.o=src/%.cpp) $(DIST_NAME)/src
	@ cp `sed -e 's/\s/\n/g' $(OBJ_DIR)/*.d | grep '^src' | sort -u` $(DIST_NAME)/src
	@ cp LICENSE_1_0.txt $(DIST_NAME)
	@ cp README-DSSP.txt $(DIST_NAME)/README.txt
	@ cp doc/mkdssp.1 $(DIST_NAME)/doc/mkdssp.1
	tar czf $(DIST_NAME).tgz $(DIST_NAME)
	cp $(DIST_NAME).tgz dssp_$(VERSION).orig.tar.gz

make.config:
	@echo "creating empty make.config file"
	@echo "# Set local options for make here" > make.config
	@echo "#BOOST_LIB_SUFFIX = -mt" >> make.config
	@echo "#BOOST_LIB_DIR    = $(HOME)/projects/boost/lib" >> make.config
	@echo "#BOOST_INC_DIR    = $(HOME)/projects/boost/include" >> make.config

