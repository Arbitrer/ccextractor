SHELL = /bin/sh

CC = gcc
CXX = g++

CFLAGS = -O3 -std=gnu99
INCLUDE = -I../src/gpacmp4/ -I../src/libpng -I../src/zlib
ALL_FLAGS = -Wno-write-strings -DGPAC_CONFIG_LINUX -D_FILE_OFFSET_BITS=64 
LDFLAGS = -lm -zmuldefs

TARGET = ccextractor

OBJS_DIR = objs
VPATH = ../src:../src/gpacmp4:../src/libpng:../src/zlib

SRCS_DIR = ../src
SRCS_C = $(wildcard $(SRCS_DIR)/*.c)
OBJS = $(SRCS_C:$(SRCS_DIR)/%.c=$(OBJS_DIR)/%.o)

SRCS_PNG_DIR = $(SRCS_DIR)/libpng
SRCS_PNG = $(wildcard $(SRCS_PNG_DIR)/*.c)
OBJS_PNG = $(SRCS_PNG:$(SRCS_PNG_DIR)/%.c=$(OBJS_DIR)/%.o)

SRCS_GPACMP4_DIR = $(SRCS_DIR)/gpacmp4
SRCS_GPACMP4_C = $(wildcard $(SRCS_GPACMP4_DIR)/*.c)
SRCS_GPACMP4_CPP = $(wildcard $(SRCS_GPACMP4_DIR)/*.cpp)
OBJS_GPACMP4 = $(SRCS_GPACMP4_C:$(SRCS_GPACMP4_DIR)/%.c=$(OBJS_DIR)/%.o) \
			   $(SRCS_GPACMP4_CPP:$(SRCS_GPACMP4_DIR)/%.cpp=$(OBJS_DIR)/%.o)

SRCS_ZLIB_DIR = $(SRCS_DIR)/zlib
SRCS_ZLIB = $(wildcard $(SRCS_ZLIB_DIR)/*.c)
OBJS_ZLIB = $(SRCS_ZLIB:$(SRCS_ZLIB_DIR)/%.c=$(OBJS_DIR)/%.o)

INSTLALL = cp -f -p
INSTLALL_PROGRAM = $(INSTLALL)
DESTDIR = /usr/bin

ifeq ($(ENABLE_OCR),yes)
CFLAGS+=-I/usr/local/include/tesseract -I/usr/local/include/leptonica
CFLAGS+=-DENABLE_OCR
LDFLAGS+= $(shell pkg-config --libs tesseract)
endif
.PHONY: all
all: objs_dir $(TARGET) 

.PHONY: objs_dir
objs_dir: 
	mkdir -p $(OBJS_DIR)

$(TARGET): $(OBJS) $(OBJS_PNG) $(OBJS_GPACMP4) $(OBJS_ZLIB)
	$(CXX) $(ALL_FLAGS) $(CFLAGS) $(OBJS) $(OBJS_PNG) $(OBJS_GPACMP4) $(OBJS_ZLIB) $(LDFLAGS) -o $@

$(OBJS_DIR)/%.o: %.c
	$(CC) -c $(ALL_FLAGS) $(INCLUDE) $(CFLAGS) $< -o $@  

$(OBJS_DIR)/%.o: %.cpp
	$(CC) -c $(ALL_FLAGS) $(INCLUDE) $(CFLAGS) $< -o $@ -I../src/gpacmp4 

.PHONY: clean
clean:
	rm $(TARGET) 2>/dev/null || true
	rm $(OBJS_PNG) $(OBJS_ZLIB) $(OBJS_GPACMP4) $(OBJS) 2>/dev/null || true
	rm -rd $(OBJS_DIR) 2>/dev/null || true
	rm .depend 2>/dev/null || true

.PHONY: install
install: $(TARGET)
	$(INSTLALL_PROGRAM) $(TARGET) $(DESTDIR)

.PHONY: uninstall
uninstall: 
	rm -iv $(DESTDIR)/$(TARGET) 

.PHONY: depend dep
depend dep:
	$(CXX) $(CXXFLAGS) -E -MM $(SRCS_C) $(SRCS_PNG) $(SRCS_ZLIB) \
		$(SRCS_GPACMP4_C) $(SRCS_GPACMP4_CPP) |\
		sed 's/^[a-zA-Z_0-9]*.o/$(OBJS_DIR)\/&/' > .depend

-include .depend