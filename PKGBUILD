# Maintainer: Filipe Laíns (FFY00) <lains@archlinux.org>

pkgname=tribler
pkgver=v7.1.0.beta.r10.g25639ac92
_dispersy=1.0
_pymdht=12.7.0
_electrum=3.2.2
_pyipv8=ad919158f7b2d910bb03e6ff6aaf33338ab40c70 # Should have a tag
pkgrel=1
pkgdesc="Privacy enhanced BitTorrent client with P2P content discovery"
url="https://www.tribler.org/"
arch=('any')
license=('LGPL3')
depends=('python2-cryptography' 'python2-feedparser' 'python2-apsw' 'python2-cherrypy' 'python2-plyvel'
	 'python2-pillow' 'python2-pyqt5' 'qt5-svg' 'phonon-qt5-vlc' 'python2-feedparser' 'python2-chardet'
	 'python2-psutil' 'python2-meliae' 'python2-decorator' 'python2-netifaces' 'python2-requests'
	 'python2-twisted' 'libsodium' 'libtorrent-rasterbar' 'python2-m2crypto' 'python2-configobj'
	 'python2-matplotlib' 'python2-service-identity' 'python2-keyring' 'python2-keyrings-alt'
	 'python2-libnacl' 'python2-contextlib2' 'python2-zc.lockfile' 'python2-datrie' 'python2-networkx')
optdepends=('vlc: for internal video player')
makedepends=('python2-setuptools' 'git')
provides=('python2-pyipv8')
conflicts=('python2-pyipv8')
source=('tribler.tar.gz'
        "dispersy-$_dispersy.tar.gz::https://github.com/Tribler/dispersy/archive/v$_dispersy.tar.gz"
        "pymdht-$_pymdht.tar.gz::https://github.com/devos50/pymdht/archive/$_pymdht.tar.gz"
        "electrum-$_electrum.tar.gz::https://github.com/spesmilo/electrum/archive/$_electrum.tar.gz"
        "py-ipv8-$_pyipv8.tar.gz::https://github.com/Tribler/py-ipv8/archive/$_pyipv8.tar.gz")
sha512sums=('SKIP'
            'b5b0fbfedcdfab6e84c056f8eb2a36ee31dbba1f796583590ed105c5a1eb36c3327ca3b933ff827fea06dde2e537486a88e71631b25aac2b777c4119e32354ea'
            'f17413be782f5210e29cdb84b0b37e68322169240d52e8ac64097a649a66c5be8d5c00d28043b47da300f32761956c169b659eeda868a43cba35b40f946057f1'
            '3dfbd6531b3d778bd202b4db69b2c68a761bb021ba6d81b4b1a106d7fb69206a3f3c7ba1b5a869488ddc82f3adfda81205f1c86b1bddfd490586e966a342c6ce'
            'SKIP')

pkgver() {
  cd $pkgname

  git describe --tags HEAD | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  # Setup submodules
  ln -sf dispersy-$_dispersy $pkgname/Tribler/disersy
  ln -sf pymdht-$_pymdht $pkgname/Tribler/Core/DecentralizedTracking/pymdht
  ln -sf electrum-$_electrum $pkgname/electrum
  ln -sf py-ipv8-$_pyipv8 $pkgname/Tribler/py-ipv8

  cd $pkgname

  # Fix tribler path
  sed -i 's|/opt/tribler|/usr/share/tribler|g' systemd/anontunnel_helper@.service
  sed -i 's|/opt/tribler|/usr/share/tribler|g' systemd/tribler.service

  # Fix version info
  sed -e "s|version_id =.*|version_id = \"$pkgver\"|g" \
      -e "s|build_date =.*|build_date = \"$(date)\"|g" \
      -e "s|commit_id =.*|commit_id = \"$(git rev-parse --short HEAD)\"|g" \
        -i Tribler/Core/version.py
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
  ln -s Tribler/Core/CacheDB/schema_sdb_v*.sql "$pkgdir"/usr/share/tribler/Tribler

  install -dm 644 "$pkgdir"/usr/share/{applications,pixmaps}
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler.desktop "$pkgdir"/usr/share/applications
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler.xpm "$pkgdir"/usr/share/pixmaps
  install -Dm 644 Tribler/Main/Build/Ubuntu/tribler_big.xpm "$pkgdir"/usr/share/pixmaps
  install -Dm 755 debian/bin/tribler "$pkgdir"/usr/bin
  install -Dm 644 logger.conf "$pkgdir"/usr/share/tribler/
  install -Dm 644 run_tribler.py "$pkgdir"/usr/share/tribler/
  install -Dm 644 check_os.py "$pkgdir"/usr/share/tribler/

  cp -dr --no-preserve=ownership twisted "$pkgdir"/usr/share/tribler
  cp -dr --no-preserve=ownership electrum "$pkgdir"/usr/share/tribler

  # Remove test folders
  find "$pkgdir" -type d -name "test" -name "tests" -exec rm -rf {} \;

  # Install systemd files
  install -Dm 644 systemd/anontunnel_helper@.service "$pkgdir"/usr/lib/systemd/system/anontunnel_helper@.service
  install -Dm 644 systemd/tribler.service "$pkgdir"/usr/lib/systemd/system/tribler.service
}

