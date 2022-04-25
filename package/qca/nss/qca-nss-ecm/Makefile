include $(TOPDIR)/rules.mk

PKG_NAME:=qca-nss-ecm
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2022-01-27
PKG_SOURCE_URL:=https://source.codeaurora.org/quic/qsdk/oss/lklm/qca-nss-ecm
PKG_SOURCE_VERSION:=57cbc7262c2489822ff14f3cd0755108baeeef1e
PKG_MIRROR_HASH:=97e761f2600a297bad909241151305750a64dc93c27f89b8d7380daef84538fe

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-nss-ecm
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  DEPENDS:=@(TARGET_ipq60xx||TARGET_ipq806x||TARGET_ipq807x) \
	+iptables-mod-extra +kmod-bonding +kmod-ipt-conntrack \
	+kmod-pppoe +PACKAGE_kmod-qca-mcs:kmod-qca-mcs +kmod-shortcut-fe \
	+(TARGET_ipq60xx||TARGET_ipq806x||TARGET_ipq807x):kmod-qca-nss-drv
  TITLE:=QCA NSS ECM (with standard features)
  FILES:= \
	$(PKG_BUILD_DIR)/*.ko \
	$(PKG_BUILD_DIR)/examples/ecm_ae_select.ko
  KCONFIG:= \
	CONFIG_BRIDGE_NETFILTER=y \
	CONFIG_NF_CONNTRACK_EVENTS=y \
	CONFIG_NF_CONNTRACK_CHAIN_EVENTS=y \
	CONFIG_NF_CONNTRACK_DSCPREMARK_EXT=n
  PROVIDES:=kmod-qca-nss-ecm
endef

define KernelPackage/qca-nss-ecm/Description
This package contains the QCA NSS Enhanced Connection Manager
endef

define KernelPackage/qca-nss-ecm/install
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/qca-nss-ecm.uci $(1)/etc/config/ecm
	$(INSTALL_DIR) $(1)/etc/firewall.d
	$(INSTALL_DATA) ./files/qca-nss-ecm.firewall $(1)/etc/firewall.d/qca-nss-ecm
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/qca-nss-ecm.init $(1)/etc/init.d/qca-nss-ecm
	$(INSTALL_DIR) $(1)/etc/sysctl.d
	$(INSTALL_BIN) ./files/qca-nss-ecm.sysctl $(1)/etc/sysctl.d/qca-nss-ecm.conf
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_DATA) ./files/qca-nss-ecm.defaults $(1)/etc/uci-defaults/99-qca-nss-ecm
	$(INSTALL_DIR) $(1)/lib/netifd/offload
	$(INSTALL_BIN) ./files/on-demand-down $(1)/lib/netifd/offload/on-demand-down
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) ./files/ecm_dump.sh $(1)/usr/bin/
endef

define KernelPackage/fast-classifier
  $(call KernelPackage/qca-nss-ecm)
  DEPENDS:=+iptables-mod-extra +kmod-pppoe \
	+kmod-ipt-conntrack +kmod-shortcut-fe
  TITLE:=QCA NSS ECM (with no-nss for host mode)
endef

define KernelPackage/fast-classifier/Description
$(call KernelPackage/qca-nss-ecm/Description)
with the No-NSS features enabled
endef

define KernelPackage/fast-classifier/install
$(KernelPackage/qca-nss-ecm/install)
endef

EXTRA_CFLAGS+= \
	-I$(STAGING_DIR)/usr/include/qca-nss-sfe

ifneq ($(CONFIG_IPV6),)
ECM_MAKE_OPTS+=ECM_IPV6_ENABLE=y
endif

ECM_MAKE_OPTS+=ECM_FRONT_END_SFE_ENABLE=y

ifneq ($(CONFIG_PACKAGE_kmod-macvlan),)
ECM_MAKE_OPTS+=ECM_INTERFACE_MACVLAN_ENABLE=y
endif

# ifneq ($(CONFIG_PACKAGE_kmod-nat46),)
# ECM_MAKE_OPTS+=ECM_INTERFACE_MAP_T_ENABLE=y
# endif

ifeq ($(CONFIG_PACKAGE_kmod-qca-mcs), y)
ECM_MAKE_OPTS+=ECM_MULTICAST_ENABLE=y
EXTRA_CFLAGS+= -I$(STAGING_DIR)/usr/include/qca-mcs
endif

ifneq ($(CONFIG_PACKAGE_kmod-qca-nss-ecm),)
ECM_MAKE_OPTS+= \
  ECM_FRONT_END_NSS_ENABLE=y \
  ECM_INTERFACE_BOND_ENABLE=y \
  ECM_NON_PORTED_SUPPORT_ENABLE=y
EXTRA_CFLAGS+= -I$(STAGING_DIR)/usr/include/qca-nss-drv
endif

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-ecm
	$(CP) $(PKG_BUILD_DIR)/exports/* $(1)/usr/include/qca-nss-ecm
endef

define Build/Compile
	$(MAKE) -C "$(LINUX_DIR)" $(strip $(ECM_MAKE_OPTS)) \
		$(KERNEL_MAKE_FLAGS) \
		M="$(PKG_BUILD_DIR)" \
		SoC="$(CONFIG_TARGET_BOARD)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		modules
endef

$(eval $(call KernelPackage,qca-nss-ecm))
$(eval $(call KernelPackage,fast-classifier))
