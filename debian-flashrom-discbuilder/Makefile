CONFIG_MIN ?= yes
CONFIG_MIRROR ?= no
CONFIG_CUSTOM_SPLASH ?= no

USE_ARCH ?= i386
USE_BUILD ?= 486
USE_DISTRO ?= lenny

#INCLUDE_PACKAGES := dhcp3-client
LIST_PACKAGELISTS := standard \
	pkglist_cli_utils \
	pkglist_devel \
	pkglist_filesystem \
	pkglist_hardware \
	pkglist_network
LIST_PACKAGELISTS += pkglist_extra

LH_OPTS := --architecture $(USE_ARCH)
LH_OPTS += --linux-flavours $(USE_BUILD)
LH_OPTS += --apt-recommends disabled
LH_OPTS += --binary-indices disabled
LH_OPTS += --debian-installer disabled
LH_OPTS += --distribution $(USE_DISTRO)
LH_OPTS += --iso-application "Flashrom Live CD"
LH_OPTS += --iso-preparer "Sean Nelson"
LH_OPTS += --iso-publisher "Flashrom.org"
LH_OPTS += --iso-volume "Flashrom Live CD - 20091214"
#LH_OPTS += --packages "$(INCLUDE_PACKAGES)"
LH_OPTS += --username flashrom
LH_OPTS += --hostname flashrom-live
LH_OPTS += --checksums disabled

ifeq ($(CONFIG_MIN), yes)
LH_OPTS += --bootstrap-flavour minimal
LH_OPTS += --apt apt
endif

ifeq ($(CONFIG_MIRROR), yes)
LH_OPTS += --mirror-bootstrap "http://debian.osuosl.org/debian/"
LH_OPTS += --mirror-chroot "http://debian.osuosl.org/debian/"
LH_OPTS += --mirror-binary "http://debian.osuosl.org/debian/"
endif

ifeq ($(CONFIG_MIN), yes)
LH_OPTS += --bootappend-live "vga=791 nolocales"
else
LH_OPTS += --bootappend-live "vga=791"
endif

ifeq ($(CONFIG_CUSTOM_SPLASH), yes)
LH_OPTS += --syslinux-splash "coreboot-splash.png"
endif

LH_OPTS_USB := --chroot-filesystem squashfs


default: help

all: clean build

prepare: init depend conf


init:
	lh config
	
help:
	@echo "Usage: make [all] | [tools] [init] [depend] [conf] [build] "
	
tools:
	apt-get install live-helper live-initramfs up	
	apt-get install genisoimage wodim 
	apt-get install mtools dosfstools squashfs-tools genext2fs mtd-tools
	apt-get install win32-loader memtest86+ memtest86 # hwtools

depend:
	cp flashrom-pkglists/pkglist* config/chroot_local-packageslists
	cp flashrom-pkgs/*.deb config/chroot_local-packages
	cp flashrom-hooks/* config/chroot_local-hooks

conf: conf-iso-gui

conf-iso-gui:
	lh config $(LH_OPTS) --packages-lists "$(LIST_PACKAGELISTS) pkglist_xgui" --binary-images iso
	
conf-iso-cli:
	lh config $(LH_OPTS) --packages-lists "$(LIST_PACKAGELISTS)" --binary-images iso
	
conf-usb-gui:
	lh config $(LH_OPTS) --packages-lists "$(LIST_PACKAGELISTS) pkglist_xgui" --binary-images usb-hdd
	
conf-usb-cli:
	lh config $(LH_OPTS) --packages-lists "$(LIST_PACKAGELISTS)" --binary-images usb-hdd
	
build:
	@export MKSQUASHFS_OPTIONS="-e boot"
	lh build

clean:
	lh clean
	
purge:
	lh clean --purge

custom-syslinux-splash:
	ppmtolss16 '#d0d0d0=7' < splash.ppm > splash.rle
	cp splash.rle config/binary_syslinux/splash.rle
	
custom-grub-splash:
	ppmtoxpm < splash.ppm > splash.xpm && gzip splash.xpm
	cp splash.xpm.gz config/binary_grub/splash.xpm.gz
