# Maintainer: crab2313 <crab2313@gmail.com>
# Contributor: Stefan Agner <stefan@agner.ch>

pkgname=qemu-user-static
pkgdesc="Statically linked binaries of Qemu with user emulation. Useful for containers/chroot environment with binfmt."
pkgver=7.0.0
pkgrel=2
arch=(x86_64 arm armv6h armv7h aarch64)
license=(GPL2 LGPL2.1)
url="http://wiki.qemu.org/"
depends=()
makedepends=(python meson glib2-static pcre-static)
source=(https://download.qemu.org/qemu-$pkgver.tar.xz{,.sig}
        qemu-7.0.0-glibc-2.36.patch)
sha512sums=('44ecd10c018a3763e1bc87d1d35b98890d0d5636acd69fe9b5cadf5024d5af6a31684d60cbe1c3370e02986434c1fb0ad99224e0e6f6fe7eda169992508157b1'
            'SKIP'
            'aa976c84d8524bb40ada506f80c01384c49f2e48fba889b0e535285aee700937886e935d18ad83b38093ba07f38be50b0fae76a0b29fd487daeac31e3221381e')
validpgpkeys=('CEACC9E15534EBABB82D3FA03353C9CEF108B584')

case $CARCH in
  i?86) _corearch=i386 ;;
  x86_64) _corearch=x86_64 ;;
esac

# If non empty, always run the configure script
: "${FORCE_CONFIGURE:=1}"

prepare() {
  mkdir -p build-user-static

  cd qemu-${pkgver}
  #sed -i 's/vte-2\.90/vte-2.91/g' configure

  patch -Np1 -d . -i ../qemu-7.0.0-glibc-2.36.patch
}

_configure() {
    ../qemu-${pkgver}/configure \
            --prefix=/usr \
            --sysconfdir=/etc \
            --localstatedir=/var \
            --libexecdir=/usr/lib/qemu \
            --enable-linux-user \
            --disable-debug-info \
            --disable-bsd-user \
            --disable-werror \
            --disable-system \
            --disable-tools \
            --disable-docs \
            --disable-gtk \
            --disable-gnutls \
            --disable-nettle \
            --disable-gcrypt \
            --disable-glusterfs \
            --disable-libnfs \
            --disable-libiscsi \
            --disable-vnc \
            --disable-kvm \
            --disable-libssh \
            --disable-vde \
            --disable-sdl \
            --disable-opengl \
            --disable-xen \
            --disable-fdt \
            --disable-vhost-net \
            --disable-vhost-crypto \
            --disable-vhost-user \
            --disable-vhost-vsock \
            --disable-vhost-scsi \
            --disable-tpm \
            --disable-qom-cast-debug \
            --disable-capstone \
            --disable-zstd \
            --disable-linux-io-uring \
            --disable-bpf \
            --static
}

build() (
  cd build-user-static
  [[ ! $FORCE_CONFIGURE && -e ../qemu-${pkgver}/config.log ]] || _configure
  make ARFLAGS="rc"
)

package() {
  provides=(qemu-user-static)
  options=(!strip)

  make -C build-user-static DESTDIR="$pkgdir" install "${@:2}"

  # remove conflicting /var/run directory
  cd "$pkgdir"
  rm -rf var

  # Remove BIOS files etc...
  rm -rf usr/share

  # Rename static qemu binaries
  cd "${pkgdir}/usr/bin/"
  tidy_strip
  ls -1 | while read f; do
    mv "$f" "$f-static"
  done
}

# vim:set ts=2 sw=2 et:
