#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# A Makefile that supports two systems namely HOST and MSP432
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
# PLATFORM-OVERRIDES:
#    CPU -  ARM Cortex Architecture(cortex-m4)
#    ARCH - ARM Architecture (thumb)
#    SPECS - Specs file to linker (nosys.specs)
# Build Targets:
#    (c1m2.o) - builds  object file
#    (c1m2.i) - generates preprocessed output
#    (c1m2.asm) - generates assembly output
#NAME:ATHARVA.R.JOSHI
#DATE:
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = HOST

#GENERAL FLAGS
GENFLAGS= -Wall \
          -Werror \
          -g \
          -std=c99 \
          -O0
NAME=c1m2
TARGET= $(NAME).out

ifeq ($(PLATFORM), MSP432) 
        #ARCHITECTURE SPECIFIC FLAGS
        LINKER_FILE = ../msp432p401r.lds
        CPU = cortex-m4
        ARCH = armv7e-m
        SPECS = nosys.specs
        FPU = fpv4-sp-d16
        EXTRA = -mthumb \
		-mfloat-abi=hard \
		-mfpu=fpv4-sp-d16
        #PLATFROM COMPILER FLAGS
        CC = arm-none-eabi-gcc
        LD = arm-none-eabi-ld
        CFLAGS = $(GENFLAGS) $(EXTRA) -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS)
        LDFLAGS =  -Xlinker -Map=$(NAME).map -T $(LINKER_FILE)
        CPPFLAGS = -DMSP432 $(INCLUDES)
	SIZE = arm-none-eabi-size
	OBJDUMP	= arm-none-eabi-objdump
        #ARHITECHTURE SPECIFIC SOURCE CODES
	SRCS   = \
	    	 main.c \
   	       	 memory.c \
		 interrupts_msp432p401r_gcc.c \
		 startup_msp432p401r_gcc.c \
		 system_msp432p401r.c

	else
		# PLATFORM COMPILER FLAGS
		CC = gcc
		CFLAGS = $(GENFLAGS) 
		LDFLAGS = -Xlinker -Map=$(NAME).map
		CPPFLAGS = -DHOST $(INCLUDES)
		OBJDUMP = objdump
		#ARCHITECHTURE SPECIFIC SOURCE CODES
		SRCS = \
	 		main.c \
	 		memory.c 
		SIZE = size 	 	
endif


OBJS=$(SRCS:.c=.o)
ASMS=$(SRCS:.c=.asm)
DEPS=$(SRCS:.c=.d)
PREP=$(SRCS:.c=.i)

#GENERATES ALL OBJECT FILES 
%.o: %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@ 

#GENERATES ALL PREPROCESSED FILES
%.i: %.c
	$(CC) -E $< $(CPPFLAGS) -o $@

#GENERATES ASSEMBLY FILES
%.asm: %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

#GENERATES DEPENDENCY FILES
%.d: %.c
	$(CC) -E -M $< $(CPPLAGS) -o $@

#BUILD ALL FILES
.PHONY: Build
build: $(NAME).out

$(NAME).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(OBJDUMP) -d $(TARGET) >> $(NAME).asm
	$(SIZE) $@
#GENERATES ALL OBJECT FILES
.PHONY: compile-all
compile-all: $(OBJS)
#CLEANS ALL GENERATED FILES 
.PHONY: Clean
clean: 
	rm -rf $(DEPS) $(PREP) $(ASMS) $(OBJS) $(NAME).out $(NAME).map $(NAME).asm


