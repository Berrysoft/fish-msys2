pkgname=fish
pkgver=4.0.1
pkgrel=9
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
makedepends=('gcc' 'gettext-devel' 'intltool' 'pcre2-devel' 'cmake' 'pkgconf')
optdepends=('python: for manual page completion parser and web configuration tool')
install=fish.install
backup=('etc/fish/config.fish' 'etc/fish/msys2.fish' 'etc/fish/perlbin.fish')
source=("https://github.com/fish-shell/fish-shell/releases/download/$pkgver/fish-$pkgver.tar.xz"
        0001-support-cygwin.patch
        0002-patch-dep.patch
        config.fish
        msys2.fish
        msystem.fish
        perlbin.fish)
sha256sums=('4ed63a70207283e15cf21fc5f21db4230421e4f98d4826b5b1c8254d69c762b5'
            '41b5aa71df398a481f502a753f1f1e4da660035b4b2f96349efd60411c3d5379'
            'd42813e834b4005ab62289ef2af151912a5dc3bfd91020b1bec05c26706e88a5'
            '983c3273e0249957ed6c40785e005739da30f31d4f029383f257f9990d38811a'
            '8bb0d28df47b66e6785f7db00a2c4316bc15960e67bdec0daca7f811f5bf3895'
            'b1a7b7b4238170373dd8acdc36bcbd1fc3978b3525403b877576139d6090e30d'
            'b136a9fa94abf53e302f7a1cc28def03b58dd2326990c5f02ceb4988341a5ac6')

prepare() {
  cd "$srcdir/$pkgname-$pkgver"
  patch -Np1 -i $srcdir/0001-support-cygwin.patch
  patch -Np1 -i $srcdir/0002-patch-dep.patch

  ~/.cargo/bin/cargo update
}

build() {
  mkdir -p $srcdir/build
  cd $srcdir/build

  export CXXFLAGS+=" ${CPPFLAGS}"
  export MSYS2_ENV_CONV_EXCL='DOCDIR;DATADIR;SYSCONFDIR;BINDIR;LOCALEDIR;PREFIX'
  export FISH_BUILD_VERSION=${pkgver}-Berrysoft-${pkgrel}
  export PKG_CONFIG_ALLOW_CROSS=1
  export CARGO_TARGET_X86_64_PC_CYGWIN_LINKER=x86_64-pc-msys-gcc
  export MSYS="winsymlinks:native"
  MSYS2_ARG_CONV_EXCL='*' cmake $srcdir/$pkgname-$pkgver \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_SYSCONFDIR=/etc \
    -DCMAKE_BUILD_TYPE=Release \
    -DRust_CARGO_TARGET=x86_64-pc-cygwin \
    -DRust_RESOLVE_RUSTUP_TOOLCHAINS=OFF \
    -DCARGO_FLAGS="-Zbuild-std" \
    -Wno-dev

  make VERBOSE=1
}

package() {
  cd $srcdir/build

  export MSYS="winsymlinks:native"
  make DESTDIR="${pkgdir}" install

  install -D -m644 "${srcdir}/config.fish" "${pkgdir}/etc/fish/config.fish"
  install -D -m644 "${srcdir}/msys2.fish" "${pkgdir}/etc/fish/msys2.fish"
  install -D -m644 "${srcdir}/msystem.fish" "${pkgdir}/etc/fish/msystem.fish"
  install -D -m644 "${srcdir}/perlbin.fish" "${pkgdir}/etc/fish/perlbin.fish"
}
