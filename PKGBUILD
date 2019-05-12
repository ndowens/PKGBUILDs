# Maintainer: willemw <willemw12@gmail.com>
# Contributor: Oliver Rümpelein <arch@pheerai.de>

pkgname=mergerfs
pkgver=2.26.0
pkgrel=1
pkgdesc="FUSE based union filesystem"
arch=('x86_64')
url="https://github.com/trapexit/mergerfs"
license=('custom:ISC')
# NOTE: fuse3 is not a dependency of mergerfs.
#       fuse2 depends on fuse-common, which has a shared library dependency on fuse3 (ldd /usr/bin/mount.fuse | grep libfuse3).
depends=('fuse2' 'fuse3')
makedepends=('git')    # 'pandoc'
#source=("https://github.com/trapexit/mergerfs/archive/$pkgver.tar.gz")
source=("https://github.com/trapexit/mergerfs/releases/download/$pkgver/mergerfs-$pkgver.tar.gz")
md5sums=('36a216cb33ea4d1759ab0bf79188356a')

build() {
  cd $pkgname-$pkgver
  make DESTDIR="$pkgdir" PREFIX="/usr" SBINDIR="/usr/bin"
}

package() {
  cd $pkgname-$pkgver
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  make DESTDIR="$pkgdir" PREFIX="/usr" SBINDIR="/usr/bin" install
}

