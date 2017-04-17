# Maintainer: Iwan Timmer <irtimmer@gmail.com>

pkgname=('anbox-git' 'anbox-modules-dkms-git')
_pkgname=anbox
pkgver=466
pkgrel=2
arch=('x86_64')
url="http://anbox.io/"
license=('GPL3')
makedepends=('cmake' 'git' 'glm' 'dbus-cpp' 'lxc' 'sdl2' 'protobuf' 'gmock' 'boost')
source=("git+https://github.com/anbox/anbox.git"
	'anbox-container-manager.service'
	'anbox-container-bridge.service'
	'anbox-session-manager.service'
	'anbox-bridge.sh'
	'99-anbox.rules'
	'anbox.conf'
	'anbox.desktop')
sha256sums=('SKIP'
	    'SKIP'
            'SKIP'
            'SKIP'
	    'SKIP' 
	    'SKIP'
            'SKIP'	
            'SKIP')

pkgver() {
  cd ${srcdir}/${_pkgname}
  git rev-list --count HEAD
}

prepare() {
  cd "$srcdir/${_pkgname}"

  # Don't build tests
  truncate -s 0 tests/CMakeLists.txt

  # Fix loading translators
  sed -i 's/${CMAKE_INSTALL_PREFIX}\/${ANBOX_TRANSLATOR_INSTALL_DIR}/${ANBOX_TRANSLATOR_INSTALL_DIR}/' CMakeLists.txt
}

build() {
  mkdir -p "$srcdir/${_pkgname}/build"
  cd "$srcdir/${_pkgname}/build"

  cmake .. -DCMAKE_INSTALL_LIBDIR=/usr/lib -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release
  make
}

package_anbox-git() {
  depends=('dbus-cpp' 'lxc' 'sdl2' 'protobuf' 'anbox-image')
  optdepends=('anbox-modules-dkms-git: Required Android kernel modules')
  pkgdesc="Running Android in a container"

  cd "$srcdir/${_pkgname}"
  make -C build DESTDIR="$pkgdir" install

  install -Dm 644 -t $pkgdir/usr/lib/systemd/system $srcdir/anbox-container-manager.service
  install -Dm 644 -t $pkgdir/usr/lib/systemd/system $srcdir/anbox-container-bridge.service
  install -Dm 644 -t $pkgdir/usr/lib/systemd/user $srcdir/anbox-session-manager.service
  install -Dm 644 -t $pkgdir/usr/lib/udev/rules.d $srcdir/99-anbox.rules
  install -Dm 644 -t $pkgdir/usr/share/applications $srcdir/anbox.desktop
  install -Dm 644 snap/gui/icon.png $pkgdir/usr/share/pixmaps/anbox.png
  install -Dm 755 -t $pkgdir/usr/bin $srcdir/anbox-bridge.sh	
}

package_anbox-modules-dkms-git() {
  pkgdesc="Required kernel module sources for Android"
  depends=('dkms')

  cd "$srcdir/${_pkgname}"
  modules=(ashmem binder)
  for mod in "${modules[@]}"; do
    install -dm 755 $pkgdir/usr/src
    cp -a kernel/$mod $pkgdir/usr/src/anbox-modules-$mod-$pkgver
  done;

  install -Dm 644 -t $pkgdir/etc/modules-load.d $srcdir/anbox.conf
}
