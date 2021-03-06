SHELL=/bin/bash

CC=i686-w64-mingw32-gcc
CFLAGS=-Wall -Wno-unknown-pragmas -Werror -O3
LDFLAGS=-shared
SRCDIR=../../src
DSTDIR=../../SoundDesignToolkit

SDTDIR=$(SRCDIR)/SDT
SDTSRCS=$(wildcard $(SDTDIR)/*.c)
SDTOBJS=$(patsubst %.c,%.o,$(SDTSRCS))

MAXDIR=$(SRCDIR)/Max6
MAXSRCS=$(wildcard $(MAXDIR)/*.c)
MAXOBJS=$(patsubst %.c,%.mxe,$(MAXSRCS))

MAXSDKDIR=../../3rdparty/Max6SDK
MAXSDKVERSION=6.1.4

PDDIR=$(SRCDIR)/Pd
PDSRCS=$(wildcard $(PDDIR)/*.c)
PDOBJS=$(patsubst %.c,%.o,$(PDSRCS))

PDSDKDIR=../../3rdparty/Pd
PDSDKVERSION=0.41.4

all: core max pd

pd: $(SDTOBJS) $(PDOBJS)
	$(CC) $(LDFLAGS) -L$(SDTDIR) -L$(PDSDKDIR) $(SDTOBJS) $(PDOBJS) -o $(PDDIR)/SDT.dll -lSDT -lpd

$(PDDIR)/%.o: $(PDDIR)/%.c
	$(CC) $(CFLAGS) -I$(SRCDIR) -I$(PDSDKDIR) -c $< -o $@

max: core $(MAXOBJS)

$(MAXDIR)/%.mxe: EXTNAME=$(patsubst $(MAXDIR)/%.mxe,%,$@)
$(MAXDIR)/%.mxe: $(MAXDIR)/%.o
	cp Info.def $(MAXDIR)/$(EXTNAME).def
	sed -i $(MAXDIR)/$(EXTNAME).def -e s/\$${PRODUCT_NAME}/$(EXTNAME)/g 
	$(CC) $(LDFLAGS) -L$(MAXSDKDIR)/max-includes -L$(MAXSDKDIR)/msp-includes -L$(MAXSDKDIR)/jit-includes -L$(SDTDIR) \
	$< $(MAXDIR)/$(EXTNAME).def -o $@ -lMaxAPI -lMaxAudio -lSDT

$(MAXDIR)/%.o: $(MAXDIR)/%.c
	$(CC) $(CFLAGS) -DDENORM_WANT_FIX=1 -DNO_TRANSLATION_SUPPORT=1 -DC74_NO_DEPRECATION \
	-DWIN_VERSION -DWIN_EXT_VERSION -fvisibility=hidden \
	-I$(MAXSDKDIR)/max-includes -I$(MAXSDKDIR)/msp-includes -I$(MAXSDKDIR)/jit-includes \
	-I$(SRCDIR) -c $< -o $@

core: $(SDTOBJS)
	$(CC) $(LDFLAGS) $(SDTOBJS) -o $(SDTDIR)/libSDT.dll

$(SDTDIR)/%.o: $(SDTDIR)/%.c
	$(CC) $(CFLAGS) -c $< -o $@

install_max:
	@if [ -z "$(DSTDIR)" ]; \
	then echo "Usage: make install_max DSTDIR=<installation_path>"; \
	elif [ ! -d "$(DSTDIR)" ]; \
	then echo "Error: installation path does not exist or is not a directory"; \
	else mkdir -p "$(DSTDIR)"/SDT/{externals,support} && \
	     cp -a ../../Max6/* "$(DSTDIR)"/SDT && \
	     cp -a $(SDTDIR)/libSDT.dll "$(DSTDIR)"/SDT/support && \
	     cp -a $(MAXDIR)/*.mxe "$(DSTDIR)"/SDT/externals && \
	     echo "Sound Design Toolkit installed in $(DSTDIR)"; \
	fi
	
install_pd:
	@if [ -z "$(DSTDIR)" ]; \
        then echo "Usage: make install_pd DSTDIR=<installation_path>"; \
        elif [ ! -d "$(DSTDIR)" ]; \
        then echo "Error: installation path does not exist or is not a directory"; \
	else mkdir -p "$(DSTDIR)"/SDT && \
	     cp -a ../../Pd/* "$(DSTDIR)"/SDT && \
	     cp -a $(PDDIR)/SDT.dll "$(DSTDIR)"/SDT && \
	     echo "Sound Design Toolkit for PureData installed in $(DSTDIR)"; \
	fi

install_core:
	@if [ -z "$(DSTDIR)" ]; \
		then echo "Usage: make install_core DSTDIR=<installation_path>"; \
		elif [ ! -d "$(DSTDIR)" ]; \
		then echo "Error: installation path does not exist or is not a directory"; \
	else mkdir -p "$(DSTDIR)"/SDT && \
		cp -a $(SDTDIR)/libSDT.dll "$(DSTDIR)"/SDT && \
		echo "Sound Design Toolkit core library installed in $(DSTDIR)"; \
	fi

clean:
	rm -rf $(SDTDIR)/libSDT.dll
	rm -rf $(SDTDIR)/*.o
	rm -rf $(MAXDIR)/*.mxe
	rm -rf $(MAXDIR)/*.def
	rm -rf $(MAXDIR)/*.o
	rm -rf $(PDDIR)/*.dll
	rm -rf $(PDDIR)/*.o
