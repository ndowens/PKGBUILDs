# Maintainer: Jose Riha <jose1711 gmail com>
pkgname=controllermap
pkgver=2.0.3
pkgrel=2
pkgdesc="Game controller mapping generator"
arch=('i686' 'x86_64')
url="http://www.libsdl.org"
license=('MIT')
depends=('sdl2')
source=("http://www.libsdl.org/release/SDL2-${pkgver}.tar.gz")
md5sums=('fe6c61d2e9df9ef570e7e80c6e822537')

build() {
cd $srcdir/SDL2-2.0.3/test
sed -i '/bmp/s%"\([^"][^"]*\)"%"/usr/share/controllermap/\1"%' controllermap.c
gcc -lSDL2 -I/usr/include/SDL2  -o controllermap controllermap.c
}

package() {
install -Dm755 $srcdir/SDL2-2.0.3/test/controllermap $pkgdir/usr/bin/controllermap
install -Dm644 $srcdir/SDL2-2.0.3/test/controllermap.bmp $pkgdir/usr/share/controllermap/controllermap.bmp
install -Dm644 $srcdir/SDL2-2.0.3/test/axis.bmp $pkgdir/usr/share/controllermap/axis.bmp
install -Dm644 $srcdir/SDL2-2.0.3/test/button.bmp $pkgdir/usr/share/controllermap/button.bmp
}
