include $(TOPDIR)/rules.mk

PKG_NAME:=qca-nss-sfe
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2022-01-24
PKG_SOURCE_URL:=https://source.codeaurora.org/quic/qsdk/oss/lklm/shortcut-fe
PKG_SOURCE_VERSION:=173b5841e8a38363361fed9c6bc1f3d8b19d695f
PKG_MIRROR_HASH:=skip

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/shortcut-fe
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  TITLE:=Kernel data path for SFE
  FILES:=$(PKG_BUILD_DIR)/qca-nss-sfe.ko
  KCONFIG:= \
	CONFIG_SHORTCUT_FE=y
	CONFIG_NF_CONNTRACK_EVENTS=y
  AUTOLOAD:=$(call AutoLoad,09,qca-nss-sfe)
endef

define KernelPackage/shortcut-fe/Description
Shortcut is an in-Linux-kernel IP packet forwarding engine.
endef

ifeq ($(CONFIG_IPV6),y)
SFE_MAKE_OPTS:=SFE_SUPPORT_IPV6=y
endif

SFE_MAKE_OPTS+=SFE_PROCESS_LOCAL_OUT=y

EXTRA_CFLAGS+= -I$(PKG_BUILD_DIR)/exports

define Build/Compile
	$(MAKE) -C "$(LINUX_DIR)"  $(strip $(SFE_MAKE_OPTS))\
		$(KERNEL_MAKE_FLAGS) \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		modules
endef

ifneq ($(CONFIG_PACKAGE_kmod-shortcut-fe),)
define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-sfe
	$(CP) -rf $(PKG_BUILD_DIR)/exports/sfe_api.h $(1)/usr/include/qca-nss-sfe
endef
endif

define KernelPackage/shortcut-fe/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) ./files/usr/bin/sfe_dump $(1)/usr/bin
endef

$(eval $(call KernelPackage,shortcut-fe))
