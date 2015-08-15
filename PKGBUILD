# Maintainer: X0rg

_gitname=darling
pkgname=$_gitname-multilib-git
pkgver=674.8ef0983
pkgrel=10
pkgdesc="A Wine-like emulation layer that enables you to run Darwin/OS X apps on Linux for multilib"
arch=('x86_64')
url="http://www.darlinghq.org"
license=('GPL3')
depends=('libbsd' 'libsystemd' 'openssl' 'gnustep-corebase-multilib-clang-svn' \
	'lib32-libbsd' 'lib32-systemd'  'lib32-openssl')
makedepends=('git' 'clang35' 'nasm' 'libkqueue' 'gnustep-gui-multilib-clang-svn')
optdepends=('dmg2dir-git: extract OS X application from .dmg file')
provides=('darling-git')
conflicts=('darling-git')
install=merge.install
backup=(etc/darling/dylib.conf)
source=('git://github.com/LubosD/darling.git'
	#'git://github.com/LubosD/darling-mach.git' Not used
	'dyld')
md5sums=('SKIP'
         '9313524c87a422c9405ece68bd4ff4f2')

pkgver() {
  cd "$srcdir/$_gitname"
  echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

prepare() {
  #msg2 "Clone submodules..."
  #mv -v "$srcdir/darling-mach" "$srcdir/$_gitname/src/darling-mach"

  msg2 "Make 'build' directory..."
  mkdir -pv "$srcdir/$_gitname/build"{32,64}
}

build() {
  # 64-bit build
  cd "$srcdir/$_gitname/build64"
  msg2 "Run 'cmake' (64-bit)..."
  CC="clang" CXX="clang++" CXXFLAGS="${CXXFLAGS//-O2}" cmake .. -DSUFFIX=64 -DCMAKE_INSTALL_PREFIX=/usr -Wno-dev

  msg2 "Run 'make' (64-bit)..."
  make

  # 32-bit build
  cd "$srcdir/$_gitname/build32"
  source "/usr/share/GNUstep32/Makefiles/GNUstep.sh"
  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"

  msg2 "Run 'cmake' (32-bit)..."
  CC="clang -m32" CXX="clang++ -m32" CXXFLAGS="${CXXFLAGS//-O2}" cmake .. -DSUFFIX=32 -DCMAKE_INSTALL_PREFIX=/usr -Wno-dev

  msg2 "Run 'make' (32-bit)..."
  make
}

package() {
  # 64-bit install
  cd "$srcdir/$_gitname/build64"
  msg2 "Install (64-bit)..."
  make DESTDIR="$pkgdir" install
  mv -v "$pkgdir/usr/lib64" "$pkgdir/usr/lib"

  # 32-bit install
  cd "$srcdir/$_gitname/build32"
  msg2 "Install (32-bit)..."
  make DESTDIR="$pkgdir" install

  msg2 "Install script dyld (auto-choosing for dyld32 or dyld64)..."
  install -Dvm755 "$srcdir/dyld" "$pkgdir/usr/bin/dyld"
}
