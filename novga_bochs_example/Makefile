.POSIX:


OBJ_EXT ?= .o
OUT_EXT ?= .img
GAS ?= gcc
GAS_EXT ?= .S
NASM_EXT ?= .asm
LD ?= ld
RUN ?= bios_hello_world
RUN_FILE := $(RUN)$(OUT_EXT)
LINKER_SCRIPT ?= linker.ld

OUTS := $(sort $(foreach IN_EXT,$(NASM_EXT) $(GAS_EXT),$(patsubst %$(IN_EXT),%$(OUT_EXT),$(wildcard *$(IN_EXT)))))

.PRECIOUS: %$(OBJ_EXT)
.PHONY: all clean doc run


all: $(OUTS)

%$(OUT_EXT): %$(OBJ_EXT) $(LINKER_SCRIPT)
	@# Failed attempt at getting debug symbols.
	@#$(LD) -melf_i386 -o '$(@:$(OUT_EXT)=.elf)' -T '$(LINKER_SCRIPT)' '$<'
	$(LD) --oformat binary -o '$@' -T '$(LINKER_SCRIPT)' '$<'

%$(OBJ_EXT): %$(GAS_EXT) $(COMMON)
	$(GAS) -c -g -o '$@' '$<'

bochs: $(RUN_FILE)
	# Supposes size is already multiples of 512.
	# We force that with our linker script,
	# and `grub-mkrescue` also seems to respect it as well.
	bochs -qf /dev/null 'ata0-master: type=disk, path="bios_hello_world.img", mode=flat, cylinders='"$CYLINDERS"', heads=1, spt=1' 'boot: disk' 'display_library: x' 'megs: 128'   'com1: enabled=1, mode=file, dev=serial.out' 'vgaromimage: file=/dev/null'




clean:
	rm -fr  *$(OBJ_EXT) *$(OUT_EXT)  serial.out

