# Maintainer: loathingkernel <loathingkernel _a_ gmail _d_ com>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: Eduardo Romero <eduardo@archlinux.org>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>

pkgname=wine-ge-custom
_srctag=GE-Proton9-5
pkgver=${_srctag//-/.}
pkgrel=1
epoch=1

_pkgbasever=${pkgver/rc/-rc}
_winever=$_pkgbasever
#_winever=${_pkgbasever%.*}

source=(wine-ge-custom::git+https://github.com/loathingKernel/wine-ge-custom.git#tag=${_srctag}
        30-win32-aliases.conf
        wine-binfmt.conf)
source+=(
        wine-wmclass.patch
        wine-isolate_home.patch
)
sha512sums=('1ddc28026d4b54f585e1fb953f0a223fa6433ed7115ea16b50d29a5c6ba5b69da5d25c29ccc6036fb831dfff98f47b2eeb903869f740b702b0eb7aac6a658d31'
            '6e54ece7ec7022b3c9d94ad64bdf1017338da16c618966e8baf398e6f18f80f7b0576edf1d1da47ed77b96d577e4cbb2bb0156b0b11c183a0accf22654b0a2bb'
            'bdde7ae015d8a98ba55e84b86dc05aca1d4f8de85be7e4bd6187054bfe4ac83b5a20538945b63fb073caab78022141e9545685e4e3698c97ff173cf30859e285')
sha512sums+=(
            '30437d8ee92c5741fa50a7fe346ccfc48ba809dad0d740903a05a67781d23ea38a5094038a070a253e3fdd8046783b46a5420df6361bdd30cb229d3d88107569'
            '3dcdbd523fcbe79b9e9e9b026b9d0a5edf296514c7b48bd465d2dc05a8ca08e23ba8817e2de08edfe52286a2a2f81db42b65f71254cabe496752b9d45131d282'
)
validpgpkeys=(5AC1A08B03BD7A313E0A955AF5E6E9EEB9461DD7
              DA23579A74D4AD9AF9D3F945CEFAC8EAAF17519D)

pkgdesc="A compatibility layer for running Windows programs - GloriousEggroll branch"
url="https://github.com/GloriousEggroll/wine-ge-custom"
arch=(x86_64 x86_64_v3)
options=(!staticlibs !lto !debug)
license=(LGPL-2.1-or-later)

depends=(
  attr             lib32-attr
  fontconfig       lib32-fontconfig
  libxcursor       lib32-libxcursor
  libxrandr        lib32-libxrandr
  libxi            lib32-libxi
  gettext          lib32-gettext
  freetype2        lib32-freetype2
  gcc-libs         lib32-gcc-libs
  libpcap          lib32-libpcap
  desktop-file-utils
)
depends+=(
  libxkbcommon     lib32-libxkbcommon
  wayland          lib32-wayland
)

makedepends=(autoconf bison perl flex mingw-w64-gcc
  git
  python
  giflib                lib32-giflib
  gnutls                lib32-gnutls
  libxinerama           lib32-libxinerama
  libxcomposite         lib32-libxcomposite
  libxxf86vm            lib32-libxxf86vm
  v4l-utils             lib32-v4l-utils
  alsa-lib              lib32-alsa-lib
  libxcomposite         lib32-libxcomposite
  mesa                  lib32-mesa
  mesa-libgl            lib32-mesa-libgl
  opencl-icd-loader     lib32-opencl-icd-loader
  libpulse              lib32-libpulse
  libva                 lib32-libva
  gtk3                  lib32-gtk3
  gst-plugins-base-libs lib32-gst-plugins-base-libs
  gst-plugins-good      lib32-gst-plugins-good
  vulkan-icd-loader     lib32-vulkan-icd-loader
  sdl2                  lib32-sdl2
  libcups               lib32-libcups
  sane
  libgphoto2
  ffmpeg
  samba
  opencl-headers
)

optdepends=(
  giflib                lib32-giflib
  gnutls                lib32-gnutls
  v4l-utils             lib32-v4l-utils
  libpulse              lib32-libpulse
  alsa-plugins          lib32-alsa-plugins
  alsa-lib              lib32-alsa-lib
  libxcomposite         lib32-libxcomposite
  libxinerama           lib32-libxinerama
  opencl-icd-loader     lib32-opencl-icd-loader
  libva                 lib32-libva
  gtk3                  lib32-gtk3
  gst-plugins-base-libs lib32-gst-plugins-base-libs
  gst-plugins-good      lib32-gst-plugins-good
  vulkan-icd-loader     lib32-vulkan-icd-loader
  sdl2                  lib32-sdl2
  sane
  libgphoto2
  ffmpeg
  cups
  samba           dosbox
)

provides=("wine=9.0")
conflicts=('wine')
install=wine.install

prepare() {
  # Get rid of old build dirs
  rm -rf $pkgname-{32,64}-build
  mkdir $pkgname-{32,64}-build

  pushd $pkgname
    git remote set-url origin https://github.com/loathingKernel/wine-ge-custom.git
    git submodule update --init --filter=tree:0 --recursive wine wine-staging
    ./patches/protonprep-lutris-staging.sh
    pushd wine
      patch -p1 -i "$srcdir"/wine-wmclass.patch
      patch -p1 -i "$srcdir"/wine-isolate_home.patch
      git config user.email "makepkg@aur.not"
      git config user.name "makepkg aur"
      git tag wine-ge-9.0 --annotate -m "$pkgver"
      ./tools/make_specfiles
      ./tools/make_requests
      ./dlls/winevulkan/make_vulkan
      autoreconf -f
    popd
  popd
}

build() {
  # Doesn't compile without remove these flags as of 4.10
  export CFLAGS="$CFLAGS -ffat-lto-objects"

  local -a split=($CFLAGS)
  local -A flags
  for opt in "${split[@]}"; do flags["${opt%%=*}"]="${opt##*=}"; done
  local march="${flags["-march"]:-nocona}"
  local mtune="generic" #"${flags["-mtune"]:-core-avx2}"

  # From Proton
  OPTIMIZE_FLAGS="-O3 -march=$march -mtune=$mtune -mfpmath=sse -pipe -fno-semantic-interposition"
  SANITY_FLAGS="-fwrapv -fno-strict-aliasing"
  WARNING_FLAGS="-Wno-incompatible-pointer-types"
  COMMON_FLAGS="$OPTIMIZE_FLAGS $SANITY_FLAGS $WARNING_FLAGS -s"

  export LDFLAGS="-Wl,-O1,--sort-common,--as-needed"
  export CROSSLDFLAGS="$LDFLAGS -Wl,--file-alignment,4096"

  cd "$srcdir"

  msg2 "Building Wine-64..."

  export CFLAGS="$COMMON_FLAGS -mcmodel=small"
  export CXXFLAGS="$COMMON_FLAGS -mcmodel=small -std=c++17"
  export CROSSCFLAGS="$CFLAGS"
  export CROSSCXXFLAGS="$CXXFLAGS"
  export PKG_CONFIG_PATH="/usr/lib/pkgconfig:/usr/share/pkgconfig"
  cd "$srcdir/$pkgname-64-build"
  ../$pkgname/wine/configure \
    --prefix=/usr \
    --libdir=/usr/lib \
    --with-x \
    --with-wayland \
    --with-gstreamer \
    --with-mingw \
    --with-alsa \
    --without-oss \
    --disable-winemenubuilder \
    --disable-tests \
    --enable-win64 \
    --with-xattr

  make

  msg2 "Building Wine-32..."

  # Disable AVX instead of using 02, for 32bit
  export CFLAGS="$COMMON_FLAGS -mstackrealign -mno-avx"
  export CXXFLAGS="$COMMON_FLAGS -mstackrealign -mno-avx -std=c++17"
  export CROSSCFLAGS="$CFLAGS"
  export CROSSCXXFLAGS="$CXXFLAGS"
  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig:/usr/share/pkgconfig"
  cd "$srcdir/$pkgname-32-build"
  ../$pkgname/wine/configure \
    --prefix=/usr \
    --with-x \
    --with-wayland \
    --with-gstreamer \
    --with-mingw \
    --with-alsa \
    --without-oss \
    --disable-winemenubuilder \
    --disable-tests \
    --with-xattr \
    --libdir=/usr/lib32 \
    --with-wine64="$srcdir/$pkgname-64-build"

  make
}

package() {
  msg2 "Packaging Wine-32..."
  cd "$srcdir/$pkgname-32-build"

  make prefix="$pkgdir/usr" \
    libdir="$pkgdir/usr/lib32" \
    dlldir="$pkgdir/usr/lib32/wine" install

  msg2 "Packaging Wine-64..."
  cd "$srcdir/$pkgname-64-build"
  make prefix="$pkgdir/usr" \
    libdir="$pkgdir/usr/lib" \
    dlldir="$pkgdir/usr/lib/wine" install

  # Font aliasing settings for Win32 applications
  install -d "$pkgdir"/usr/share/fontconfig/conf.{avail,default}
  install -m644 "$srcdir/30-win32-aliases.conf" "$pkgdir/usr/share/fontconfig/conf.avail"
  ln -s ../conf.avail/30-win32-aliases.conf "$pkgdir/usr/share/fontconfig/conf.default/30-win32-aliases.conf"
  install -Dm 644 "$srcdir/wine-binfmt.conf" "$pkgdir/usr/lib/binfmt.d/wine.conf"

  i686-w64-mingw32-strip --strip-unneeded "$pkgdir"/usr/lib32/wine/i386-windows/*.{dll,exe}
  x86_64-w64-mingw32-strip --strip-unneeded "$pkgdir"/usr/lib/wine/x86_64-windows/*.{dll,exe}

  find "$pkgdir"/usr/lib{,32}/wine -iname "*.a" -delete
  find "$pkgdir"/usr/lib{,32}/wine -iname "*.def" -delete
}

# vim:set ts=8 sts=2 sw=2 et:
