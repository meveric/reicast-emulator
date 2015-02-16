
LOCAL_PATH := $(call my-dir)
FOR_LINUX :=1
NOT_ARM := 1
NO_REC := 1
#NO_REND := 1
NO_NIXPROF := 1
RZDCY_SRC_DIR = ../../core

include $(RZDCY_SRC_DIR)/core.mk

CC_PREFIX ?= 

CXX=${CC_PREFIX}em++
CC=${CC_PREFIX}emcc
#AS=${CC_PREFIX}as
STRIP=${CC_PREFIX}emstrip

LD=${CC}

MFLAGS := 
ASFLAGS := 

LDFLAGS	:=	-g -Wl,-Map,$(notdir $@).map,--gc-sections -Wl,-O3 -Wl,--sort-common 



CXXFLAGS := -g -O3  -D GLES -D RELEASE -c -D TARGET_GCW0 -D HOST_EMSCRIPTEN -D HOST_NO_REC -D HOST_NO_NVMEM -D HOST_NO_WEBUI -D HOST_NO_THREADS
CXXFLAGS += -fno-strict-aliasing
CXXFLAGS += -ffast-math

CXXFLAGS += $(CFLAGS) $(MFLAGS) -fno-exceptions -fno-rtti


ifdef PGO_MAKE
	CXXFLAGS += -fprofile-generate -pg
	LDFLAGS	+= -fprofile-generate
else
	CXXFLAGS += -fomit-frame-pointer
endif

ifdef PGO_USE
	CXXFLAGS += -fprofile-use
endif


ifdef LTO_TEST
	CXXFLAGS += -flto -fwhole-program 
	LDFLAGS +=-flto -fwhole-program 
endif

INCS	:= -I$(RZDCY_SRC_DIR) -I$(RZDCY_SRC_DIR)/deps -I$(RZDCY_SRC_DIR)/khronos -I../linux-deps/include

LIBS    := -L../linux-deps/lib -L./enta_viv 
LIBS    += -lEGL -lGLESv2
LIBS    += -lasound


OBJECTS=$(RZDCY_FILES:.cpp=.build.obj)
OBJECTS:=$(OBJECTS:.c=.build.obj)
OBJECTS:=$(OBJECTS:.S=.build.obj)
OBJECTS:=$(patsubst $(RZDCY_SRC_DIR)/%,obj/%,$(OBJECTS))


PACKAGE=reicast-gcwz.opk
EXECUTABLE_STRIPPED=nosym-reicast.elf
EXECUTABLE_TARBZ=reicast.tar.bz2
EXECUTABLE=reicast.html


all: $(CPPFILES) $(EXECUTABLE)
	
$(EXECUTABLE): $(OBJECTS)
	echo $(RZDCY_FILES)
	$(CXX) $(MFLAGS) $(EXTRAFLAGS) $(LDFLAGS)  $(OBJECTS) $(LIBS) -o $@ --preload-file data/dc_boot.bin --preload-file data/dc_flash.bin -s TOTAL_MEMORY=96000000

obj/%.build.obj : $(RZDCY_SRC_DIR)/%.cpp
	mkdir -p $(dir $@)
	$(CXX) $(EXTRAFLAGS) $(INCS) $(CXXFLAGS) $< -o $@
	
obj/%.build.obj : $(RZDCY_SRC_DIR)/%.c
	mkdir -p $(dir $@)
	$(CC) $(EXTRAFLAGS) $(INCS) $(CXXFLAGS) $< -o $@

obj/%.build.obj : $(RZDCY_SRC_DIR)/%.S
	mkdir -p $(dir $@)	
	$(AS) $(ASFLAGS) $(INCS) $< -o $@


clean:
	rm $(OBJECTS) $(EXECUTABLE) -f
