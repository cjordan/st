pkgname=st-git
_pkgname=st
pkgver=20151011.f56c58a
pkgrel=1
pkgdesc='Simple virtual terminal emulator for X'
url='http://git.suckless.org/st/'
arch=('i686' 'x86_64' 'armv7h')
license=('MIT')
depends=('libxft')
makedepends=('ncurses' 'libxext' 'git')
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
    cp config.def.h config.h
    for p in ../../patches/{0..9}*.diff; do
        [ -f $p ] || continue
        echo "=> $p"
        patch < $p || return 1
    done

    sed \
        -e 's/CPPFLAGS =/CPPFLAGS +=/g' \
        -e 's/CFLAGS =/CFLAGS +=/g' \
        -e 's/LDFLAGS =/LDFLAGS +=/g' \
        -e 's/_BSD_SOURCE/_DEFAULT_SOURCE/' \
        -e 's/-Os/-O2/g' \
        -e 's/# CC = cc/CC = clang/g' \
        -i config.mk
    sed '/@tic/d' -i Makefile

    # Fix st terminfo colliding with ncurses-6
    sed \
        -e 's/st-/st-git-/g' \
        -e 's/st|/st-git|/g' \
        -i st.info
}

build() {
    cd "${srcdir}/${_pkgname}"
    make X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
    cd "${srcdir}/${_pkgname}"
    make PREFIX=/usr DESTDIR="${pkgdir}" install
    install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 README "${pkgdir}/usr/share/doc/${pkgname}/README"
    tic -s -o "${pkgdir}/usr/share/terminfo" st.info
}
