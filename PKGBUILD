# Maintainer: Filipe Laíns (FFY00) <lains@archlinux.org>

pkgname=tribler
pkgrel=1
pkgver=7.3.0
pkgdesc="Privacy enhanced BitTorrent client with P2P content discovery"
url="https://www.tribler.org/"
arch=('any')
license=('LGPL3')
depends=('python2-cryptography' 'python2-cherrypy'
	 'python2-pillow' 'python2-pyqt5' 'qt5-svg' 'phonon-qt5-vlc' 'python2-chardet'
	 'python2-psutil' 'python2-meliae' 'python2-decorator' 'python2-netifaces'
	 'python2-twisted' 'libsodium' 'libtorrent-rasterbar' 'python2-configobj'
	 'python2-matplotlib' 'python2-service-identity' 'python2-pony'
	 'python2-libnacl' 'python2-contextlib2' 'python2-zc.lockfile' 'python2-networkx')
optdepends=('vlc: for internal video player' 'python2-bitcoinlib')
makedepends=('python2-setuptools' 'git')
source=('tribler.tar.gz')
sha512sums=('SKIP')

pkgver() {
  cd $pkgname

  git describe --tags HEAD | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd $pkgname

  # Fix tribler path
  sed -i 's|/opt/tribler|/usr/share/tribler|g' systemd/anontunnel_helper@.service
  sed -i 's|/opt/tribler|/usr/share/tribler|g' systemd/tribler.service
}

build () {
  cd $pkgname

  python2 setup.py build
}

package() {
  cd $pkgname

  # Install python modules
  python2 setup.py install --root="$pkgdir" --optimize=1 --skip-build

  # Install binary files/assets
  install -dm 755 "$pkgdir"/usr/{bin,share/tribler}
  cp -dr --no-preserve=ownership Tribler "$pkgdir"/usr/share/tribler
  cp -dr --no-preserve=ownership TriblerGUI "$pkgdir"/usr/share/tribler

  install -dm 644 "$pkgdir"/usr/share/{applications,pixmaps}
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler.desktop "$pkgdir"/usr/share/applications
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler.xpm "$pkgdir"/usr/share/pixmaps
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler_big.xpm "$pkgdir"/usr/share/pixmaps
  install -Dm 755 debian/bin/tribler "$pkgdir"/usr/bin
  install -Dm 644 logger.conf "$pkgdir"/usr/share/tribler/
  install -Dm 644 run_tribler.py "$pkgdir"/usr/share/tribler/
  install -Dm 644 check_os.py "$pkgdir"/usr/share/tribler/

  cp -dr --no-preserve=ownership twisted "$pkgdir"/usr/share/tribler

  # Remove test folders
  find "$pkgdir" -type d -name "test" -name "tests" -exec rm -rf {} \;

  # Install systemd files
  install -Dm 644 systemd/anontunnel_helper@.service "$pkgdir"/usr/lib/systemd/system/anontunnel_helper@.service
  install -Dm 644 systemd/tribler.service "$pkgdir"/usr/lib/systemd/system/tribler.service
}

