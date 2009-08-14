# $Id$
# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Maintainer: Dan McGee <dan@archlinux.org>

pkgname=pacman
pkgver=3.3.0
pkgrel=3
pkgdesc="A library-based package manager with dependency support"
arch=('macx86')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('libarchive>=2.7.0-2' 'libfetch>=2.20' 'pacman-mirrorlist' 'gettext')
optdepends=('fakeroot: for makepkg usage as normal user'
            'python: for rankmirrors script usage'
            'coreutils: for repo-add')
backup=(opt/arch/etc/pacman.conf opt/arch/etc/makepkg.conf opt/arch/etc/pacman.d/mirrorlist)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        makepkg.conf
        repo_add_sedspaces.patch
            # Patch repo-add to work wtih BSD/sed or GNU/sed
        stat_full_path.patch
            # Patch to use full path of stat in case user has coreutils
        makepkg_sed.patch
            # Patch makepkg to work with BSD/sed or GNU/sed
        )

build() {
  cd $srcdir/$pkgname-$pkgver
  
  patch -p0 < $startdir/stat_full_path.patch
  patch -p0 < $startdir/repo_add_sedspaces.patch
  patch -p0 < $startdir/makepkg_sed.patch
  sed -i~ -e 's/--strip-debug/-S/' scripts/makepkg.sh.in # Work with Mac's strip
  ./configure --prefix=/opt/arch
  make || return 1
  make DESTDIR=$pkgdir install || return 1

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  install -m644 $srcdir/pacman.conf $pkgdir/etc/
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  case "$CARCH" in
    i686)
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686 "
      ;;
    x86_64)
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64 "
      ;;
  esac
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  mkdir -p $pkgdir/opt/arch/etc/bash_completion.d/
  install -m644 contrib/bash_completion $pkgdir/opt/arch/etc/bash_completion.d/pacman
  mkdir -p $pkgdir/opt/arch/share/zsh/site-functions/
  install -m644 contrib/zsh_completion $pkgdir/opt/arch/share/zsh/site-functions/_pacman
}

# vim: set ts=2 sw=2 et:
