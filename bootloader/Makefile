.PHONY: clean help
.PHONY: all uboot 
.PHONY: all_ram atf_ram
.PHONY: all_emmc atf_emmc

TOPDIR := $(CURDIR)
AARCH64_CROSS_COMPILE ?= aarch64-linux-gnu-
MT_PLAT ?= mt7981

OUTPUT_DIR = $(TOPDIR)/output
BUILD_DIR = $(OUTPUT_DIR)/build
BUILD_UBOOT_PATH = $(BUILD_DIR)/u-boot
BUILD_ATF_PATH = $(BUILD_DIR)/atf

MKIMG_FILE = $(BUILD_UBOOT_PATH)/tools/mkimage
BL33_FILE =  $(BUILD_UBOOT_PATH)/u-boot.bin
BL2_FILE = $(BUILD_ATF_PATH)/$(MT_PLAT)/release/bl2.bin
FIP_FILE = $(BUILD_ATF_PATH)/$(MT_PLAT)/release/fip.bin

BL2_IMG = $(BUILD_ATF_PATH)/$(MT_PLAT)/release/bl2.img

UBOOT_CONFIG := mt7981_rax3000m_defconfig
UBOOT_RAM_CONFIG := mt7981_rax3000m_ram_defconfig

.DEFAULT_GOAL := all

all: all_ram all_emmc
all_ram: clean uboot_ram atf_ram
all_emmc: clean uboot_emmc atf_emmc

uboot_ram:
	$(MAKE) -C u-boot O=$(BUILD_UBOOT_PATH) $(UBOOT_RAM_CONFIG)
	$(MAKE) -C u-boot O=$(BUILD_UBOOT_PATH) CROSS_COMPILE=$(AARCH64_CROSS_COMPILE)

atf_ram:
	$(MAKE) -C atf BUILD_BASE=$(BUILD_ATF_PATH) CROSS_COMPILE=$(AARCH64_CROSS_COMPILE) PLAT=$(MT_PLAT) MKIMAGE=$(MKIMG_FILE) BL33=$(BL33_FILE) BOOT_DEVICE=ram USE_MKIMAGE=1 BOARD_BGA=1 HAVE_DRAM_OBJ_FILE=yes DRAM_USE_DDR4=1 RAM_BOOT_UART_DL=1 all fip
	dd if=$(BL2_FILE) of=$(OUTPUT_DIR)/bl2-ram.bin
	dd if=$(FIP_FILE) of=$(OUTPUT_DIR)/fip-ram.bin

uboot_emmc:
	$(MAKE) -C u-boot O=$(BUILD_UBOOT_PATH) $(UBOOT_CONFIG)
	$(MAKE) -C u-boot O=$(BUILD_UBOOT_PATH) CROSS_COMPILE=$(AARCH64_CROSS_COMPILE)

atf_emmc:
	$(MAKE) -C atf BUILD_BASE=$(BUILD_ATF_PATH) CROSS_COMPILE=$(AARCH64_CROSS_COMPILE) PLAT=$(MT_PLAT) MKIMAGE=$(MKIMG_FILE) BL33=$(BL33_FILE) BOOT_DEVICE=emmc USE_MKIMAGE=1 BOARD_BGA=1 HAVE_DRAM_OBJ_FILE=yes DRAM_USE_DDR4=1 all fip
	dd if=$(BL2_IMG) of=$(OUTPUT_DIR)/bl2-emmc.img
	dd if=$(FIP_FILE) of=$(OUTPUT_DIR)/fip-emmc.bin

clean:
	rm -rf $(BUILD_DIR)

