# AArch64 multi-platform
# Contributor: Kevin Mihelich <kevin@archlinuxarm.org>
# Maintainer: Dan Johansen <strit@manjaro.org>

_pkgname=linux-pbp
pkgbase=${_pkgname}-tux
provides=("${_pkgname}")
conflicts=("${_pkgname}")
_srcname=linux-5.8
_kernelname=${pkgbase#linux}
_desc="Customised Linux kernel with patches for the Pinebook Pro."
pkgver=5.8.14
pkgrel=1
arch=('aarch64')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('clang>=11.0.0' 'llvm>=11.0.0' 'lld>=11.0.0' 'xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'git' 'uboot-tools' 'dtc')
options=('!strip')
source=("http://www.kernel.org/pub/linux/kernel/v5.x/${_srcname}.tar.xz"
        "http://www.kernel.org/pub/linux/kernel/v5.x/patch-${pkgver}.xz"
        '0001-pwm-rockchip-Keep-enabled-PWMs-running-while-probing.patch'
        '0004-tty-serdev-support-shutdown-op.patch'
        '0005-bluetooth-hci_serdev-Clear-registered-bit-on-unregis.patch'
        '0006-bluetooth-hci_bcm-disable-power-on-shutdown.patch'
        '0007-mmc-core-pwrseq_simple-disable-mmc-power-on-shutdown.patch'
        '0010-usb-typec-tcpm-add-hacky-generic-altmode-support.patch'
        '0011-phy-rockchip-typec-Set-extcon-capabilities.patch'
        '0012-usb-typec-altmodes-displayport-Add-hacky-generic-alt.patch'
        '0018-arm64-dts-rockchip-add-cw2015-fuel-gauge.patch'
        '0021-arm64-dts-rockchip-add-typec-extcon-hack.patch'
        '0024-arm64-dts-rockchip-setup-USB-type-c-port-as-dual-dat.patch'
        'overclock.patch'
        'pbp-power-button-as-delete.patch'
        'add-support-for-Clang-LTO.patch'
        'config'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook')
md5sums=('0e5c4c15266218ef26c50fac0016095b'
         '5ee3a39d4e71c1c00d21044d4c46d1ee'
         '698946c973f5a4adbef281d760c478bd'
         '11e653f50135c1e9fa703118fa7f2623'
         '05c7919f7fc1019e99e6965559bde5d5'
         '894a88a9579b22c22747b1748f181bb9'
         '610a4e1484a1874b272ff6d8292f5931'
         'cae6d2c56b98a1dfa387987166441440'
         'b03bc41f9434564dba2ee414c679afae'
         'fe3d7fc55581ecc3be73b25d4451f81d'
         'f97ff1529a78b8eaf94922c3e8b72109'
         '8c37f21fa687479dbee0cd40299cd79c'
         '73803b563b9e794a3b495524de99ced7'
         '81d70a76169189a6f029ef717cedcf4d'
         '3fd917748d2d2843120fa6f2d56c5c5f'
         '3bf7a09ccd7ea40fb39d20744cd30d7d'
         '209659c583c7ba045d4668a881985c6b'
         '86d4a35722b5410e3b29fc92dae15d4b'
         'ce6c81ad1ad1f8b333fd6077d47abdaf'
         '3dc88030a8f2f5a5f97266d99b149f77')

prepare() {
  cd ${_srcname}

  # add upstream patch
  patch -Np1 -i "${srcdir}/patch-${pkgver}"

  patch -Np1 -i "${srcdir}/0001-pwm-rockchip-Keep-enabled-PWMs-running-while-probing.patch"
  patch -Np1 -i "${srcdir}/0004-tty-serdev-support-shutdown-op.patch"
  patch -Np1 -i "${srcdir}/0005-bluetooth-hci_serdev-Clear-registered-bit-on-unregis.patch"
  patch -Np1 -i "${srcdir}/0006-bluetooth-hci_bcm-disable-power-on-shutdown.patch"
  patch -Np1 -i "${srcdir}/0007-mmc-core-pwrseq_simple-disable-mmc-power-on-shutdown.patch"
  patch -Np1 -i "${srcdir}/0010-usb-typec-tcpm-add-hacky-generic-altmode-support.patch"
  patch -Np1 -i "${srcdir}/0011-phy-rockchip-typec-Set-extcon-capabilities.patch"
  patch -Np1 -i "${srcdir}/0012-usb-typec-altmodes-displayport-Add-hacky-generic-alt.patch"
  patch -Np1 -i "${srcdir}/0018-arm64-dts-rockchip-add-cw2015-fuel-gauge.patch"
  patch -Np1 -i "${srcdir}/0021-arm64-dts-rockchip-add-typec-extcon-hack.patch"
  patch -Np1 -i "${srcdir}/0024-arm64-dts-rockchip-setup-USB-type-c-port-as-dual-dat.patch"

  # misc. patches
  patch -Np1 -i "${srcdir}/overclock.patch"
  patch -Np1 -i "${srcdir}/pbp-power-button-as-delete.patch"
  patch -Np1 -i "${srcdir}/add-support-for-Clang-LTO.patch"

  cat "${srcdir}/config" > ./.config

  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
}

build() {
  cd ${_srcname}

  # get kernel version
  make CC=clang LLVM=1 prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  # ... or manually edit .config

  # Copy back our configuration (use with new kernel version)
  #cp ./.config /var/tmp/${pkgbase}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config

  # build!
  unset LDFLAGS
  make CC=clang LLVM=1 ${MAKEFLAGS} Image Image.gz modules
  # Generate device tree blobs with symbols to support applying device tree overlays in U-Boot
  make CC=clang LLVM=1 ${MAKEFLAGS} DTC_FLAGS="-@" dtbs
}

_package() {
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
  optdepends=('crda: to set the correct wireless channels of your country')
  provides=('kernel26' "linux=${pkgver}")
  replaces=('linux-armv8')
  conflicts=('linux')
  backup=("etc/mkinitcpio.d/${pkgbase}.preset")
  install=${pkgname}.install

  cd ${_srcname}

  KARCH=arm64

  # get kernel version
  _kernver="$(make kernelrelease)"
  _basekernel=${_kernver%%-*}
  _basekernel=${_basekernel%.*}

  mkdir -p "${pkgdir}"/{boot,usr/lib/modules}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  make INSTALL_DTBS_PATH="${pkgdir}/boot/dtbs" dtbs_install
  cp arch/$KARCH/boot/Image{,.gz} "${pkgdir}/boot"

  # make room for external modules
  local _extramodules="extramodules-${_basekernel}${_kernelname}"
  ln -s "../${_extramodules}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"

  # add real version for building modules and running depmod from hook
  echo "${_kernver}" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modules/${_extramodules}/version"

  # remove build and source links
  rm "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}

  # now we call depmod...
  depmod -b "${pkgdir}/usr" -F System.map "${_kernver}"

  # add vmlinux
  install -Dt "${pkgdir}/usr/lib/modules/${_kernver}/build" -m644 vmlinux

  # sed expression for following substitutions
  local _subst="
    s|%PKGBASE%|${pkgbase}|g
    s|%KERNVER%|${_kernver}|g
    s|%EXTRAMODULES%|${_extramodules}|g
  "

  # install mkinitcpio preset file
  sed "${_subst}" ../linux.preset |
    install -Dm644 /dev/stdin "${pkgdir}/etc/mkinitcpio.d/${pkgbase}.preset"

  # install pacman hooks
  sed "${_subst}" ../60-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/60-${pkgbase}.hook"
  sed "${_subst}" ../90-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/90-${pkgbase}.hook"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')

  cd ${_srcname}
  local _builddir="${pkgdir}/usr/lib/modules/${_kernver}/build"

  install -Dt "${_builddir}" -m644 Makefile .config Module.symvers
  install -Dt "${_builddir}/kernel" -m644 kernel/Makefile

  mkdir "${_builddir}/.tmp_versions"

  cp -t "${_builddir}" -a include scripts

  install -Dt "${_builddir}/arch/${KARCH}" -m644 arch/${KARCH}/Makefile
  install -Dt "${_builddir}/arch/${KARCH}/kernel" -m644 arch/${KARCH}/kernel/asm-offsets.s arch/$KARCH/kernel/module.lds

  cp -t "${_builddir}/arch/${KARCH}" -a arch/${KARCH}/include
  mkdir -p "${_builddir}/arch/arm"
  cp -t "${_builddir}/arch/arm" -a arch/arm/include

  install -Dt "${_builddir}/drivers/md" -m644 drivers/md/*.h
  install -Dt "${_builddir}/net/mac80211" -m644 net/mac80211/*.h

  # http://bugs.archlinux.org/task/13146
  install -Dt "${_builddir}/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # http://bugs.archlinux.org/task/20402
  install -Dt "${_builddir}/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "${_builddir}/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "${_builddir}/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # add xfs and shmem for aufs building
  mkdir -p "${_builddir}"/{fs/xfs,mm}

  # copy in Kconfig files
  find . -name Kconfig\* -exec install -Dm644 {} "${_builddir}/{}" \;

  # remove unneeded architectures
  local _arch
  for _arch in "${_builddir}"/arch/*/; do
    [[ ${_arch} == */${KARCH}/ || ${_arch} == */arm/ ]] && continue
    rm -r "${_arch}"
  done

  # remove files already in linux-docs package
  rm -r "${_builddir}/Documentation"

  # remove now broken symlinks
  find -L "${_builddir}" -type l -printf 'Removing %P\n' -delete

  # Fix permissions
  chmod -R u=rwX,go=rX "${_builddir}"

  # strip scripts directory
  local _binary _strip
  while read -rd '' _binary; do
    case "$(file -bi "${_binary}")" in
      *application/x-sharedlib*)  _strip="${STRIP_SHARED}"   ;; # Libraries (.so)
      *application/x-archive*)    _strip="${STRIP_STATIC}"   ;; # Libraries (.a)
      *application/x-executable*) _strip="${STRIP_BINARIES}" ;; # Binaries
      *) continue ;;
    esac
    /usr/bin/strip ${_strip} "${_binary}"
  done < <(find "${_builddir}/scripts" -type f -perm -u+w -print0 2>/dev/null)
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
