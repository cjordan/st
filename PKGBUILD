pkgname=st-git
_pkgname=st
pkgver=20151121.375b287
pkgrel=1
pkgdesc='A simple virtual terminal emulator for X.'
url='http://git.suckless.org/st/'
arch=('i686' 'x86_64' 'armv7h')
license=('MIT')
depends=('libxft' 'libxext')
makedepends=('ncurses' 'git')
source=('git://git.suckless.org/st')
sha1sums=('SKIP')

provides=("${_pkgname}")
conflicts=("${_pkgname}")

pkgver() {
    cd "${srcdir}/${_pkgname}"
    git log -1 --format='%cd.%h' --date=short | tr -d -
}

prepare() {
    cd "${srcdir}/${_pkgname}"
    for p in ../../patches/{0..9}*.diff; do
        [ -f $p ] || continue
        echo "=> $p"
        patch < $p || return 1
    done
    cp config.def.h config.h

    sed \
        -e 's/CPPFLAGS =/CPPFLAGS +=/g' \
        -e 's/CFLAGS =/CFLAGS +=/g' \
        -e 's/LDFLAGS =/LDFLAGS +=/g' \
        -e 's/_BSD_SOURCE/_DEFAULT_SOURCE/' \
        -e 's/-Os/-O2/g' \
        -e 's/# CC = cc/CC = clang/g' \
        -i config.mk

    # skip terminfo which conflicts with ncurses
    sed -i '/\@tic /d' Makefile
}

build() {
    cd "${srcdir}/${_pkgname}"
    make X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
    cd "${srcdir}/${_pkgname}"
    make PREFIX=/usr DESTDIR="${pkgdir}" TERMINFO="${pkgdir}/usr/share/terminfo" install
    install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 README "${pkgdir}/usr/share/doc/${pkgname}/README"
}
