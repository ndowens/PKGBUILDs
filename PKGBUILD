# Maintainer: Guillaume Horel <guillaume.horel@gmail.com>
pkgname=('python-ufonormalizer')
_pkgname='ufonormalizer'
pkgver='0.3.6'
pkgrel=1
pkgdesc="A tool that will normalize XML and other data inside of a UFO."
url="https://github.com/unified-font-object/ufoNormalizer"
checkdepends=()
depends=('python')
makedepends=('python-setuptools')
optdepends=()
license=('BSD')
arch=('any')
source=("https://pypi.org/packages/source/${_pkgname:0:1}/$_pkgname/$_pkgname-$pkgver.zip")
sha256sums=('e41978fa581feb31ff9aec10f6e52aff27ccdd1ce242529cbc51bfe862687acf')

check() {
    cd "$srcdir/$_pkgname-$pkgver"
    python setup.py test
}

package() {
    cd "${srcdir}/${_pkgname}-${pkgver}"
    python setup.py install --root="${pkgdir}" --optimize=1
    install -D -m644 LICENSE.txt "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE.txt"
}
