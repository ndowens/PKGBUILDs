# Maintainer: Eren Simsek <18117384-caferen@users.noreply.gitlab.com>
# Maintainer: Guy Boldon <gb@guyboldon.com>

pkgname=coolercontrol-liqctld
pkgver=1.4.2
pkgrel=1
pkgdesc="liquidctl daemon for CoolerControl"
arch=('x86_64')
url="https://gitlab.com/coolercontrol/coolercontrol"
license=('GPL-3.0-or-later')
depends=(
  'liquidctl'
  'python'
  'python-setproctitle'
  'python-fastapi'
  'python-pydantic'
  'uvicorn'
)
makedepends=(
  'python-build'
  'python-installer'
  'python-setuptools'
  'python-wheel'
)
provides=(
  "$pkgname"
)
conflicts=(
  "$pkgname"
)
# when liqctld is an optional dependency we should
# we can turn the main source package to a group too
groups=(coolercontrol-bin)
source=(
  "https://gitlab.com/coolercontrol/coolercontrol/-/archive/$pkgver/coolercontrol-$pkgver.tar.gz"
)
sha256sums=(
  '0185f95f000cf2a587ceecde9aadaf58901afe9a35fdc56acf7e1df81c56b13e'
)

build() {
  cd "${srcdir}/coolercontrol-$pkgver/$pkgname"
  python -m build --wheel --no-isolation
}

check() {
  cd "${srcdir}/coolercontrol-$pkgver/$pkgname"
  python -m coolercontrol_liqctld --version
}

package() {
  cd "${srcdir}/coolercontrol-$pkgver/$pkgname"
  python -m installer --destdir="$pkgdir" dist/*.whl

  cd "${srcdir}/coolercontrol-$pkgver"
  # systemd service files
  install -Dm644 "packaging/systemd/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system/"

  install -Dm644 README.md -t "$pkgdir/usr/share/doc/$pkgname"
  install -Dm644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname"
}
