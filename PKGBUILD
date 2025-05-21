pkgname=fish
pkgver=4.0.2
pkgrel=5
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
makedepends=('gcc' 'gettext-devel' 'intltool' 'pcre2-devel' 'cmake' 'pkgconf' 'rust')
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
sha256sums=('6e1ecdb164285fc057b2f35acbdc20815c1623099e7bb47bbfc011120adf7e83'
            '8b94656c9f6118c19efa2bcb960b447863ff8b9b1f6ee957e00d6d87d8ef79f0'
            'b2739eee8f030f468eaf435982ac50056738f5a7bfefe5288cd13430f57498bc'
            '983c3273e0249957ed6c40785e005739da30f31d4f029383f257f9990d38811a'
            '8bb0d28df47b66e6785f7db00a2c4316bc15960e67bdec0daca7f811f5bf3895'
            'b1a7b7b4238170373dd8acdc36bcbd1fc3978b3525403b877576139d6090e30d'
            'b136a9fa94abf53e302f7a1cc28def03b58dd2326990c5f02ceb4988341a5ac6')

prepare() {
  cd "$srcdir/$pkgname-$pkgver"
  patch -Np1 -i $srcdir/0001-support-cygwin.patch
  patch -Np1 -i $srcdir/0002-patch-dep.patch

  /usr/bin/cargo update
}

build() {
  mkdir -p $srcdir/build
  cd $srcdir/build

  export CXXFLAGS+=" ${CPPFLAGS}"
  export FISH_BUILD_VERSION=${pkgver}-Berrysoft-${pkgrel}
  export CARGO_TARGET_X86_64_PC_CYGWIN_LINKER=x86_64-pc-msys-gcc
  export MSYS="winsymlinks:native"
  cmake $srcdir/$pkgname-$pkgver \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_SYSCONFDIR=/etc \
    -DCMAKE_BUILD_TYPE=Release \
    -DRust_COMPILER=/usr/bin/rustc \
    -DRust_CARGO=/usr/bin/cargo \
    -DRust_CARGO_TARGET=x86_64-pc-cygwin \
    -DRust_CARGO_HOST_TARGET=x86_64-pc-cygwin \
    -DRust_RESOLVE_RUSTUP_TOOLCHAINS=OFF \
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
