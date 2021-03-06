# Copyright 2009,2016 Ronald S. Burkey <info@sandroid.org>
#
# This file is part of yaAGC.
#
# yaAGC is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# yaAGC is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with yaAGC; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
#
# Filename:	Makefile
# Purpose:	This makefile is used to build the sterm program.
# Mods:		2009-03-19 RSB	Adapted trivially from yaDEDA2's makefile.
#		2009-05-02 RSB	Added DEV_SNAP.
#		2016-08-06 RSB  This had been set to build sterm statically-linked
#				for USERNAME=rburkey (me!) and dynamically-linked
#				for everyone else.  That's because I would often
#				build it for the purpose of making an installer,
#				and it was easier if the executables were statically
#				linked to the extent possible.  But it turns out 
#				that the standard library function gethostbyname()
#				used by agc_utilities.c no longer works (at least on
#		                some Linux distributions) if statically 
#		                linked.   I made this change because I made a 
#				similar fix for yaAGC, and not because I noticed any
#				problems with with this app ... but they must have 
#				been there!

APPNAME=sterm

# For cross-platform building --- i.e., for building Win32 and Mac OS X versions
# of ${APPNAME} on a Linux platform --- it is assumed that the IMCROSS
# environment (www.sandroid.org/imcross) is installed.  It is further supposed
# that the IMCROSS installation directory is ~/IMCROSS and that ~/IMCROSS/bin 
# is in the PATH.  The following variables just reflect the default setup for
# IMCROSS.

# IMCROSS compilation is default but also allow native builds
# To build native on a platform set the NATIVE environment variable
# to either WIN32,MACOSX or LINUX

ifdef NATIVE

PREFIX_WIN=
BIN_WIN=

else

PREFIX_WIN=i386-mingw32-
PREFIX_MAC=powerpc-apple-darwin9-
BIN_WIN=${HOME}/IMCROSS/i386-mingw32/bin/
BIN_MAC=${HOME}/IMCROSS/mac/bin/
INC_MAC=${HOME}/IMCROSS/mac/include
SDK_MAC=${HOME}/IMCROSS/mac/SDKs/MacOSX10.4u.sdk

endif

# This is just so that when I build it, I can insure that it will be statically or
# dynamically linked in the binary installers, according to my current whims.
# For anybody else, it will just accept whatever your wxWidgets default library is.
ifeq "${USER}" "rburkey"
ifdef DEV_STATIC
WXSTATIC=--static
endif
endif

.PHONY: default
default: Linux

.PHONY: all-archs
all-archs: Linux Win32 MacOsX

.PHONY: Linux
Linux: ${APPNAME}

.PHONY: Win32
Win32: ${APPNAME}.exe

.PHONY: MacOsX
MacOsX: ${APPNAME}-macosx

.PHONY: clean
clean:
	-rm -rf temp
	-rm -f *~ *.bak ${APPNAME} *.exe *-macosx*

SOURCES:=${APPNAME}.cpp \
	../yaAGC/agc_utilities.c \
	../yaAGC/agc_engine.c \
	../yaAGC/Backtrace.c \
	../yaAGC/random.c \
	../yaAGC/SocketAPI.c \
	../yaAGC/rfopen.c

${APPNAME}: ${SOURCES}
	g++ \
		`wx-config --cxxflags` \
		-o $@ $^ \
		`wx-config --libs`
	strip $@${EXT}

${APPNAME}.exe: ${SOURCES}
	${PREFIX_WIN}g++ \
		`${BIN_WIN}wx-config ${WXSTATIC} --cxxflags` \
		-o $@ $^ `${BIN_WIN}wx-config ${WXSTATIC} --libs`
	${PREFIX_WIN}strip $@

${APPNAME}-macosx: ${SOURCES}
	powerpc-apple-darwin9-g++ \
		-arch ppc -I${INC_MAC} -isysroot ${SDK_MAC} \
		-I${SDK_MAC}/usr/include/c++/4.0.0 \
		-I${SDK_MAC}/usr/include/c++/4.0.0/powerpc-apple-darwin9 \
		`${BIN_MAC}wx-config ${WXSTATIC} --cxxflags` \
		-o $@-ppc $^ \
		`${BIN_MAC}wx-config ${WXSTATIC} --libs`
	powerpc-apple-darwin9-lipo \
		$@-ppc \
		-remove i386 \
		-output $@-ppc-a		
	i686-apple-darwin9-g++ \
		-arch i386 -I${INC_MAC} -isysroot ${SDK_MAC} \
		-I${SDK_MAC}/usr/include/c++/4.0.0 \
		-I${SDK_MAC}/usr/include/c++/4.0.0/i686-apple-darwin9 \
		`${BIN_MAC}wx-config ${WXSTATIC} --cxxflags` \
		-o $@-i386 $^ \
		`${BIN_MAC}wx-config ${WXSTATIC} --libs`
	i686-apple-darwin9-lipo \
		$@-i386 \
		-remove ppc \
		-output $@-i386-a		
	${PREFIX_MAC}lipo \
		$@-ppc-a \
		$@-i386-a \
		-create \
		-output $@		
	${PREFIX_MAC}strip $@


