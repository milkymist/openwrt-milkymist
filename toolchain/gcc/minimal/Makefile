GCC_VARIANT:=minimal

include ../common.mk

GCC_CONFIGURE += \
	--with-newlib \
	--without-headers \
	--enable-languages=c \
	--disable-libssp \
	--disable-shared \
	--disable-threads

define Host/SetToolchainInfo
	$(SED) 's,TARGET_CROSS=.*,TARGET_CROSS=$(REAL_GNU_TARGET_NAME)-,' $(TOOLCHAIN_DIR)/info.mk
	$(SED) 's,GCC_VERSION=.*,GCC_VERSION=$(GCC_VERSION),' $(TOOLCHAIN_DIR)/info.mk
endef

define Host/Prepare
	$(call Host/SetToolchainInfo)
	$(call Host/Prepare/Default)
	ln -snf $(GCC_DIR) $(BUILD_DIR_TOOLCHAIN)/$(PKG_NAME)
	$(CP) $(SCRIPT_DIR)/config.{guess,sub} $(HOST_BUILD_DIR)/
	$(SED) 's,^MULTILIB_OSDIRNAMES,# MULTILIB_OSDIRNAMES,' $(HOST_BUILD_DIR)/gcc/config/*/t-*
	$(SED) 's,\(version_string.. = "[0-9\.]*\).*\(";\),\1 (OpenWrt-2.0)\2,' $(HOST_BUILD_DIR)/gcc/version.c
	$(SED) 's,\(bug_report_url.. = "\).*\(";\),\1<URL:$(BUGURL)>\2,' $(HOST_BUILD_DIR)/gcc/version.c
	$(SED) 's,http://gcc.gnu.org/bugs.html,$(BUGURL),' $(HOST_BUILD_DIR)/gcc/configure
	#(cd $(HOST_BUILD_DIR)/libstdc++-v3; autoconf;);
	$(SED) 's,gcc_no_link=yes,gcc_no_link=no,' $(HOST_BUILD_DIR)/libstdc++-v3/configure
	mkdir -p $(GCC_BUILD_DIR)
endef

define Host/Compile
	$(GCC_MAKE) -C $(GCC_BUILD_DIR) all-gcc
endef

define Host/Install
	$(GCC_MAKE) -C $(GCC_BUILD_DIR) install-gcc
endef

define Host/Clean
	rm -rf \
		$(HOST_BUILD_DIR) \
		$(GCC_BUILD_DIR)
endef

$(eval $(call HostBuild))
