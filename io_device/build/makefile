#
# build ks1 executables
#
APPLICATION_NAME = ks1
BINARY_NAME = $(APPLICATION_NAME).elf
APPLICATION_VERSION = "2020-02"
APPLICATION_UID = "'u','i','d','0','0','0','0','0','0','0','0','0','0','k','s','1'"

#
# file locations
#
IO_DIR = ../../io_core
CPU_DIR = ../../io_cpu
BOARD_DIR = ../../io_board
COMPONENT_DIR = ../../io_components
JS_DIR = ../../io_js
DEVICE_DIR = ..
DEVICE_SOURCE_DIR = $(DEVICE_DIR)/source
#
# TOOLS
#
TOOL_PREFIX = arm-none-eabi-
CC = $(TOOL_PREFIX)gcc
#
# flags
#
OPTIMISATION = -O2
WARNINGS = -Wall
DEFS = -DDEVICE_NAME="\"$(APPLICATION_NAME)\"" -DSTATIC_UID=$(APPLICATION_UID)
TARGET_FLAGS = -DSTM32F40_41xxx -mcpu=cortex-m4 -mfpu=fpv4-sp-d16 -mfloat-abi=hard -mthumb 
GENERIC_FLAGS = -fomit-frame-pointer -ffunction-sections -fdata-sections -nostdlib -nostartfiles -std=c99 -g3
COMPILE_FLAGS = $(DEFS) $(TARGET_FLAGS) $(GENERIC_FLAGS) $(OPTIMISATION) $(WARNINGS) \
	-fno-diagnostics-color \
	-Wno-address-of-packed-member \
	-Wno-unused-variable \
	-Wno-unused-but-set-variable\
	-Wfatal-errors
LINK_FLAGS = $(TARGET_FLAGS) --specs=nosys.specs -T$(DEVICE_DIR)/linker/stm32f405g.ld -Wl,--gc-sections \
	-Wl,-Map=$(APPLICATION_NAME).map
#
#
#
OBJECTS = main.o js.o test.o
DEPENDS = $(IO_DIR)/*.h \
	$(COMPONENT_DIR)/nfc/*.h\
	$(CPU_DIR)/*.h \
	$(CPU_DIR)/cmsis/*.h \
	$(JS_DIR)/io_js.h\
	$(DEVICE_DIR)/include/*.h \
	$(BOARD_DIR)/*.h \
	makefile
	
JSDEPENDS = $(JS_DIR)/*.h \
	$(JS_DIR)/quickjs/*.h $(JS_DIR)/quickjs/*.c \
	$(JS_DIR)/jsimport/std/*.h \
	$(JS_DIR)/jsimport/io/*.h $(JS_DIR)/jsimport/io/*.c

INCLUDE_DIRS = -I$(DEVICE_DIR)/include \
	-I$(IO_DIR) \
	-I$(COMPONENT_DIR) \
	-I$(CPU_DIR) \
	-I$(CPU_DIR)/cmsis \
	-I$(JS_DIR)\
	-I$(BOARD_DIR)

all: $(APPLICATION_NAME).elf binary

$(APPLICATION_NAME).elf: $(OBJECTS)  $(DEVICE_DIR)/linker/stm32f405g.ld makefile
	@echo link $<
	@$(CC) $(LINK_FLAGS) -o $@ $(OBJECTS) -lm -lgcc

js.o: $(DEVICE_SOURCE_DIR)/js.c $(DEPENDS) $(JSDEPENDS)
	@echo compile $<
	@$(CC) $(INCLUDE_DIRS) -o $@ -c $(COMPILE_FLAGS) $<

%.o: $(DEVICE_SOURCE_DIR)/%.c $(DEPENDS) $(JSDEPENDS)
	@echo compile $<
	@$(CC) $(INCLUDE_DIRS) -o $@ -c $(COMPILE_FLAGS) $<

size:
	@$(TOOL_PREFIX)size.exe $(APPLICATION_NAME).elf

binary: $(APPLICATION_NAME).elf
	@$(TOOL_PREFIX)objcopy.exe -O binary $(APPLICATION_NAME).elf $(APPLICATION_NAME).bin
	
clean:
	rm -f *.o *.map *.elf *.bin
	
.PHONY: all clean binary


