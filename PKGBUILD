# Maintainer: Cian Mc Govern <cian@cianmcgovern.com>
# Contributor: Mikael Eriksson <mikael_eriksson@miffe.org>
# Original arch package by:
# Thomas Baechler <thomas@archlinux.org>

pkgname=nvidia-f2fs
pkgver=304.60
pkgrel=1
pkgdesc="NVIDIA drivers for linux-f2fs."
read _ _kernver < <(file -b "/boot/vmlinuz-linux-f2fs" | grep -o 'version [^ ]\+')
#_kernpkgver=3.7.rc3
_kernpkgver=$(pacman -Q linux-f2fs|cut -d' ' -f 2)
_extramodules=$_kernver/extramodules
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
depends=('linux-f2fs' "nvidia-utils=${pkgver}")
conflicts=('nvidia-96xx' 'nvidia-173xx')
license=('custom')
install=nvidia.install
options=(!strip)

if [ "$CARCH" = "i686" ]; then
    _arch='x86'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}"
    source=("ftp://download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('42b9887076b2ebcf1af5ee13bc332ccb')
elif [ "$CARCH" = "x86_64" ]; then
    _arch='x86_64'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}-no-compat32"
    source=("ftp://download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('7248399a125808e3bbc9c66da99a098d')
fi

source=(${source[@]} "conftest.patch" "nv-mmap.patch")
md5sums=(${md5sums[@]} "5445c793f60adaa9299af3b1f01660a4" "e07227820c4a8d622959a1bbdd0c2a74")

build() {
    msg "Building for kernel version $_kernver"
    cd "${srcdir}"
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}/kernel"
    patch < "${srcdir}/conftest.patch"
    patch < "${srcdir}/nv-mmap.patch"
    make SYSSRC=/usr/src/"linux-${_kernver}/" module
}

package() {
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia.ko" \
        "${pkgdir}/lib/modules/${_extramodules}/nvidia.ko"
    install -d -m755 "${pkgdir}/etc/modprobe.d"
    echo "blacklist nouveau" >> "${pkgdir}/etc/modprobe.d/nvidia.conf"
    echo "blacklist nvidiafb" >> "${pkgdir}/etc/modprobe.d/nvidia.conf"
    sed -i -e "s,EXTRAMODULES='.*',EXTRAMODULES='${_extramodules}'," "${startdir}/nvidia.install"
    gzip "${pkgdir}/lib/modules/${_extramodules}/nvidia.ko"
    eval pkgrel="${pkgrel}_${_kernpkgver/-/_}"
}
