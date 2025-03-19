_realname=fish-shell
pkgname=fish
pkgver=4.0.1
pkgrel=1
epoch=
pkgdesc='Smart and user friendly shell intended mostly for interactive use'
arch=('x86_64')
url="https://fishshell.com/"
msys2_repository_url="https://github.com/fish-shell/fish-shell"
msys2_references=(
  "cpe: cpe:/a:fishshell:fish"
)
license=('spdx:GPL-2.0-only')
depends=('gcc-libs' 'gettext' 'libpcre2_32' 'libpcre2_8' 'man-db')
makedepends=('gcc' 'gettext-devel' 'intltool' 'pcre2-devel' 'cmake' 'mingw-w64-clang-x86_64-python-sphinx')
optdepends=('python: for manual page completion parser and web configuration tool')
install=fish.install
backup=('etc/fish/config.fish' 'etc/fish/msys2.fish' 'etc/fish/perlbin.fish')
source=("https://github.com/fish-shell/fish-shell/archive/refs/tags/$pkgver.tar.gz"
        0001-support-cygwin.patch
        0002-patch-dep.patch
        config.fish
        msys2.fish
        msystem.fish
        perlbin.fish)
sha256sums=('fd1b3629c0000711cadfce0e106704656e8c23fe608425f69687edda54d5f9b0'
            '4bb071ce5f7c1221636e7da434719c6411926cc9fea21ebf3dbd9440d948b9bf'
            '19bb974c026f1d22768fdde7d3a6de38d084b9e1d2a62b0780261fbca8e63f76'
            '983c3273e0249957ed6c40785e005739da30f31d4f029383f257f9990d38811a'
            '8bb0d28df47b66e6785f7db00a2c4316bc15960e67bdec0daca7f811f5bf3895'
            'b1a7b7b4238170373dd8acdc36bcbd1fc3978b3525403b877576139d6090e30d'
            'b136a9fa94abf53e302f7a1cc28def03b58dd2326990c5f02ceb4988341a5ac6')

prepare() {
  cd "$srcdir/$_realname-$pkgver"
  patch -Np1 -i $srcdir/0001-support-cygwin.patch
  patch -Np1 -i $srcdir/0002-patch-dep.patch

  cargo update
}

build() {
  mkdir -p $srcdir/build
  cd $srcdir/build

  export CXXFLAGS+=" ${CPPFLAGS}"
  export MSYS2_ENV_CONV_EXCL='DOCDIR;DATADIR;SYSCONFDIR;BINDIR;LOCALEDIR;PREFIX'
  export SPHINX_DIR=/clang64
  export FISH_BUILD_VERSION=${pkgver}-Berrysoft-${pkgrel}
  MSYS2_ARG_CONV_EXCL='*' cmake $srcdir/$_realname-$pkgver \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_SYSCONFDIR=/etc \
    -DCMAKE_BUILD_TYPE=Release \
    -DRust_CARGO_TARGET=x86_64-pc-cygwin \
    -DCARGO_FLAGS="-Zbuild-std" \
    -DBUILD_DOCS=True \
    -Wno-dev

  make VERBOSE=1
}

package() {
  cd $srcdir/build

  make DESTDIR="${pkgdir}" install

  install -D -m644 "${srcdir}/config.fish" "${pkgdir}/etc/fish/config.fish"
  install -D -m644 "${srcdir}/msys2.fish" "${pkgdir}/etc/fish/msys2.fish"
  install -D -m644 "${srcdir}/msystem.fish" "${pkgdir}/etc/fish/msystem.fish"
  install -D -m644 "${srcdir}/perlbin.fish" "${pkgdir}/etc/fish/perlbin.fish"
}
