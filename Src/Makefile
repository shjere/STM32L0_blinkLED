STM_COMMON = ../../common
PROJ_NAME = blink
OUTPATH=build

######################################################################################
# GNU GCC ARM Embeded Toolchain base directories and binaries
######################################################################################
CC=arm-none-eabi-gcc
OBJCOPY=arm-none-eabi-objcopy
SIZE=arm-none-eabi-size

# Choose debug or release...
CFLAGS = -g -O2   # Normal
CFLAGS += -mlittle-endian -mthumb -mthumb-interwork -mcpu=cortex-m0plus --specs=nosys.specs
CFLAGS += -TSTM32L053C8_FLASH.ld

################################### INCLUDE DIRECTORIES ##############################
INC_DIR = ../Inc
INC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/CMSIS/Device/ST/STM32L0xx/Include
INC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/BSP/STM32L0538-Discovery
INC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/BSP/Components/gde021a1
INC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/CMSIS/Include
INC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/STM32L0xx_HAL_Driver/Inc

#################################### SOURCE DIRECTORIES ##############################
SRC_DIR = .  
SRC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/STM32L0xx_HAL_Driver/Src/
SRC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/BSP/STM32L0538-Discovery/
SRC_DIR += $(STM_COMMON)/stm32l0discovery/Drivers/BSP/Components/gde021a1

##################################### STARTUP FILE ###################################
STARTUP_FILE = $(STM_COMMON)/startup/startup_stm32l053xx.s

################################ FIND source files in DIRs ###########################
SRCS = $(shell find $(SRC_DIR) -name '*.c' -printf "%f\n")
SRCS += $(STARTUP_FILE)

######################################################################################
# Path Setup
######################################################################################
vpath %.c $(SRC_DIR)
# Includes
CFLAGS += $(foreach d, $(INC_DIR), -I$d)
OBJS = $(SRCS:.c=.o)


######################################################################################
# Targets
######################################################################################
.PHONY: lib proj dir_tree

all:  dir_tree proj

dir_tree:
	mkdir -p $(OUTPATH)

proj: $(OUTPATH)/$(PROJ_NAME).elf
	$(SIZE) $(OUTPATH)/$(PROJ_NAME).elf

$(OUTPATH)/$(PROJ_NAME).elf: $(SRCS)
	$(CC) $(CFLAGS) $^ -o $@ $(LIBPATHS) $(LIBS)
	$(OBJCOPY) -O ihex $(OUTPATH)/$(PROJ_NAME).elf $(OUTPATH)/$(PROJ_NAME).hex
	$(OBJCOPY) -O binary $(OUTPATH)/$(PROJ_NAME).elf $(OUTPATH)/$(PROJ_NAME).bin

clean:
	find . -name \*.o -type f -delete
	find . -name \*.lst -type f -delete
	rm -f $(OUTPATH)/$(PROJ_NAME).elf
	rm -f $(OUTPATH)/$(PROJ_NAME).hex
	rm -f $(OUTPATH)/$(PROJ_NAME).bin

# Flash the STM32L0
burn: proj
	#$(STLINK)/st-flash write $(OUTPATH)/$(PROJ_NAME).bin 0x8000000

dfu: proj
	#dfu-util -v -d 0483:df11 -a 0 -s 0x08000000 -D $(OUTPATH)/$(PROJ_NAME).bin

print-%:
	@echo $* = $($*)
