include $(TOPDIR)/rules.mk

PKG_NAME:=qca-edma
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://source.codeaurora.org/quic/qsdk/oss/lklm/qca-edma
PKG_SOURCE_DATE:=2021-03-18
PKG_SOURCE_VERSION:=7e47c81b5b1678e921b7f52ddde96ec08ef5edad
PKG_MIRROR_HASH:=4281bb1472f49b451d59cbf49ddaf075092f8d2aa17c9b2b24ae697bf4687d4f

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-edma
  SUBMENU:=Network Devices
  TITLE:=QCA IPQ40xx ethernet driver
  FILES:=$(PKG_BUILD_DIR)/essedma.ko
  DEPENDS:=@TARGET_ipq40xx
  AUTOLOAD:=$(call AutoLoad,45,essedma)
endef

define KernelPackage/qca-edma/description
 Kernel modules for IPQ40xx integrated ethernet adapater.
endef

EXTRA_CFLAGS+= -I$(PKG_BUILD_DIR)

define Build/Compile
	$(MAKE) -C "$(LINUX_DIR)" \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		modules
endef

define KernelPackage/qca-edma/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/qca-edma $(1)/etc/init.d/qca-edma
endef

$(eval $(call KernelPackage,qca-edma))
