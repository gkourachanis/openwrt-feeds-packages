#
# Copyright (C) 2017 Banglang Huang
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=libpfring
PKG_VERSION:=8.3.0.r160.g82e91899
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE_NAME:=PF_RING
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/ntop/$(PKG_SOURCE_NAME).git
PKG_SOURCE_DATE:=2022-11-24
PKG_SOURCE_VERSION:=82e91899910bf136d5ebb11008688a7a21dd81d8
PKG_MIRROR_HASH:=8f0289cbde10d8e39016aae7274b9afcde3ffe27ef60bceb818be6a906319f70
PKG_SOURCE_SUBDIR:=$(PKG_SOURCE_NAME)-$(PKG_VERSION)
PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_MAINTAINER:=Banglang Huang <banglang.huang@foxmail.com>

PKG_FIXUP:=patch-libtool
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

CONFIGURE_PATH:=userland
MAKE_PATH:=userland/lib

define Package/libpfring
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Library for PR_RING (package process framework)
  URL:=https://github.com/ntop/pf_ring
  DEPENDS:=+kmod-pf-ring +libpcap +libpthread
  LICENSE:=LGPL-2.1-or-later
  LICENSE_FILES:=LICENSE
endef

define Package/libpfring/description
  PF_RING is a high speed packet capture library that turns a commodity PC into an efficient and cheap
  network measurement box suitable for both packet and active traffic analysis and manipulation.
  Moreover, PF_RING opens totally new markets as it enables the creation of efficient application such as
  traffic balancers or packet filters in a matter of lines of codes.
endef

define KernelPackage/pf-ring
  SUBMENU:=Network Support
  TITLE:=PF_RING Kernel driver
  FILES:=$(PKG_BUILD_DIR)/kernel/pf_ring.ko
  AUTOLOAD:=$(call AutoLoad,90,pf_ring,1)
  LICENSE:=GPL-2.0-or-later
endef

define KernelPackage/pf-ring/description
  Kernel module for libpf-ring package
endef

CONFIGURE_VARS += \
	MACHINE="$(ARCH)" \
	ac_cv_lib_nl_3_nl_socket_alloc=no

define Build/Compile
	$(MAKE) -C "$(LINUX_DIR)" \
		KERNEL_DIR="$(LINUX_DIR)" \
		ARCH="$(LINUX_KARCH)" \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		M="$(PKG_BUILD_DIR)/kernel" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS) -I$(PKG_BUILD_DIR)/kernel" \
		modules
	$(call Build/Compile/Default)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/* $(1)/usr/include/

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpfring.so* $(1)/usr/lib/
endef

define Package/libpfring/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpfring.so* $(1)/usr/lib/
	$(LN) libpfring.so $(1)/usr/lib/libpfring.so.1
endef

$(eval $(call BuildPackage,libpfring))
$(eval $(call KernelPackage,pf-ring))
