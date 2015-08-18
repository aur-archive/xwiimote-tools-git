# Maintainer: Swift Geek
pkgname=xwiimote-tools-git
pkgver=0
pkgrel=1
pkgdesc="Open Source Nintendo Wii Remote Linux Device Driver Tools"
url="https://github.com/dvdhrm/xwiimote"
arch=('i686' 'x86_64')
license=('GPL2')
depends=('bluez4')
provides=('xwiimote-tools' 'xwiimote-git')

_gitroot="git://github.com/dvdhrm/xwiimote.git"
_gitname="xwiimote"

prepare() {
  cd ${srcdir}
  msg "Connecting to GIT server...."

  if [ -d ${srcdir}/${_gitname} ] ; then
    cd ${_gitname} && git pull origin
    msg "The local files are updated."
  else
    git clone --depth=1 ${_gitroot}
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting make..."

  rm -rf "$srcdir/$_gitname-build"
  cp -R "$srcdir/$_gitname" "$srcdir/$_gitname-build"

  cd "$srcdir/$_gitname-build"
  # Why true? cuz pacman is crazy
  true && pkgver="$(git rev-list --count HEAD).$(git rev-parse --short HEAD)"
  export _pkgver=$pkgver
}

build() {
  cd "$srcdir/$_gitname-build"
  ./autogen.sh
  ./configure --prefix=/usr
  make
}
  
package() {
  cd ${srcdir}/${_gitname}-build/
  make DESTDIR="${pkgdir}" install
  install -Dm755 ./xwiidump "${pkgdir}"/usr/bin/
  install -D COPYING "$pkgdir/usr/share/licenses/$pkgname/COPYING"

  cd ${srcdir}/${_gitname}-build/tools/
  install -Dm755 ./xwiibind.sh "${pkgdir}"/usr/bin/

  cd ${srcdir}/${_gitname}-build/res/
  install -Dm644 ./50-xorg-fix-xwiimote.conf "${pkgdir}"/etc/X11/xorg.conf.d/50-xorg-fix-xwiimote.conf 

  sed -i 's#"simple-agent"#"bluez-simple-agent"#' \
    "${pkgdir}"/usr/bin/xwiibind.sh
  sed -i 's#"test-input"#"bluez-test-input"#' \
    "${pkgdir}"/usr/bin/xwiibind.sh
  sed -i 's#"test-device"#"bluez-test-device"#' \
    "${pkgdir}"/usr/bin/xwiibind.sh

  true && pkgver=$_pkgver
}
