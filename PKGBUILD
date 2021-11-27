# Maintainer: loathingkernel <loathingkernel @at gmail .dot com>

pkgname=vkd3d-proton-mingw-git
pkgver=2.5.r81.g9c354936
pkgrel=1
pkgdesc='Fork of VKD3D. Development branches for Protons Direct3D 12 implementation'
arch=('x86_64')
url="https://github.com/HansKristian-Work/vkd3d-proton"
license=('LGPL-2.1')
depends=('vulkan-icd-loader' 'wine>=4.0rc1' 'lib32-vulkan-icd-loader' 'bash')
makedepends=('ninja' 'meson>=0.43' 'glslang' 'git' 'mingw-w64-gcc' 'mingw-w64-tools')
provides=('vkd3d-proton' 'd3d12.dll' "vkd3d-proton=$pkgver")
conflicts=('vkd3d-proton' 'd3d12.dll')
options=(!lto !staticlibs)
source=(
    "git+https://github.com/HansKristian-Work/vkd3d-proton.git"
    "git+https://github.com/HansKristian-Work/dxil-spirv.git"
    "git+https://github.com/KhronosGroup/Vulkan-Headers.git"
    "git+https://github.com/KhronosGroup/SPIRV-Headers.git"
    "setup_vkd3d_proton"
    "vkd3d-proton-extraopts.patch"
)

pkgver() {
    cd vkd3d-proton
    printf "%s" "$(git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g;s/v//g')"
}

prepare() {
    cd vkd3d-proton

    for submodule in subprojects/{dxil-spirv,Vulkan-Headers,SPIRV-Headers}; do
        git submodule init "${submodule}"
        git config submodule."${submodule}".url "$srcdir"/"${submodule#*/}"
        git submodule update "${submodule}"
    done
    pushd subprojects/dxil-spirv
    git submodule init third_party/spirv-headers
    git config submodule.third_party/spirv-headers.url "$srcdir"/SPIRV-Headers
    git submodule update third_party/spirv-headers
    popd

    # Filter known bad flags before applying optimizations
    # Filter fstack-protector{ ,-all,-strong} flag for MingW.
    # https://github.com/Joshua-Ashton/d9vk/issues/476
    CFLAGS="${CFLAGS// -fstack-protector*([\-all|\-strong])/}"
    CXXFLAGS="${CXXFLAGS// -fstack-protector*([\-all|\-strong])/}"
    # Doesn't compile with these flags in MingW so remove them.
    # They are also filtered in Wine PKGBUILDs so remove them
    # for winelib versions too.
    CFLAGS="${CFLAGS/ -fno-plt/}"
    CXXFLAGS="${CXXFLAGS/ -fno-plt/}"
    LDFLAGS="${LDFLAGS/,-z,now/}"
    LDFLAGS="${LDFLAGS/,-z,relro/}"

    # By default export FLAGS used by proton and ignore makepkg
    # This overrides FLAGS from makepkg.conf, if you comment these you are on your own
    # If you want the "best" possible optimizations for your system you can use
    # `-march=native` and remove the `-mtune=core-avx2` option.
    # `-O2` is adjusted to `-O3` since AVX is disabled
    export CFLAGS="-O3 -march=nocona -mtune=core-avx2 -pipe"
    export CXXFLAGS="-O3 -march=nocona -mtune=core-avx2 -pipe"
    export LDFLAGS="-Wl,-O1,--sort-common,--as-needed"

    # Uncomment to enable extra optimizations
    # Patch crossfiles with extra optimizations from makepkg.conf
    patch -p1 -i "$srcdir"/vkd3d-proton-extraopts.patch
    local cross_cflags="$CFLAGS"
    local cross_cxxflags="$CXXFLAGS"
    local cross_ldflags="$LDFLAGS"

    # If using -march=native and the CPU supports AVX, launching a d3d9
    # game can cause an Unhandled exception. The cause seems to be the
    # combination of AVX instructions and tree vectorization (implied by O3),
    # all tested archictures from sandybridge to haswell are affected.
    # Disabling AVX (and AVX2 as a side-effect).
    # Since Wine 5.16 AVX is supported. Testing showed 32bit applications
    # crashing with AVX regardless, but 64bit applications worked just fine.
    # So disable AVX only for the 32bit binaries and AVX2 for the 64bit.
    # AVX2 seems to degrade performance. So disregard the above.
    # Relevant Wine issues
    # https://bugs.winehq.org/show_bug.cgi?id=45289
    # https://bugs.winehq.org/show_bug.cgi?id=43516
    cross_cflags+=" -mno-avx -mno-avx2"
    cross_cxxflags+=" -mno-avx -mno-avx2"

    # These flags are taken from Proton, I don't know if there are issues with Arch wine.
    cross_cflags+=" -mfpmath=sse -fwrapv -fno-strict-aliasing -gdwarf-2 -gstrict-dwarf"
    cross_cxxflags+=" -mfpmath=sse -fwrapv -fno-strict-aliasing -gdwarf-2 -gstrict-dwarf -std=c++17"
    cross_ldflags+=" -Wl,--file-alignment,4096"

    sed -i build-win64.txt \
        -e "s|@CARGS@|\'${cross_cflags// /\',\'}\'|g" \
        -e "s|@CXXARGS@|\'${cross_cxxflags// /\',\'}\'|g" \
        -e "s|@LDARGS@|\'${cross_ldflags// /\',\'}\'|g"

    cross_cflags+=" -mstackrealign"
    cross_cxxflags+=" -mstackrealign"
    sed -i build-win32.txt \
        -e "s|@CARGS@|\'${cross_cflags// /\',\'}\'|g" \
        -e "s|@CXXARGS@|\'${cross_cxxflags// /\',\'}\'|g" \
        -e "s|@LDARGS@|\'${cross_ldflags// /\',\'}\'|g"
}

build() {
    meson vkd3d-proton "build/x64" \
        --prefix "/usr/share/vkd3d-proton/x64" \
        --cross-file vkd3d-proton/build-win64.txt \
        --bindir "" --libdir "" \
        --buildtype "plain" \
        --strip \
        -Denable_tests=false
    ninja -C "build/x64" -v

    meson vkd3d-proton "build/x86" \
        --cross-file vkd3d-proton/build-win32.txt \
        --prefix "/usr/share/vkd3d-proton/x86" \
        --bindir "" --libdir "" \
        --buildtype "plain" \
        --strip \
        -Denable_tests=false
    ninja -C "build/x86" -v
}

package() {
    DESTDIR="$pkgdir" ninja -C "build/x86" install
    DESTDIR="$pkgdir" ninja -C "build/x64" install
    install -Dm 755 -t "$pkgdir/usr/share/vkd3d-proton" vkd3d-proton/setup_vkd3d_proton.sh
    install -Dm 644 -t "$pkgdir/usr/share/$pkgname" vkd3d-proton/LICENSE
    install -Dm 755 -t "$pkgdir/usr/bin" setup_vkd3d_proton
}

sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            'SKIP'
            '459d0de87abd6ea44ac71b4d7a005b6cfc9b2a75613ecda161223e95a22414d8'
            '8fc019d1dca8c52b6af96c40ff06a6c215aad3e713ae17be72c7422f1ba45634')

