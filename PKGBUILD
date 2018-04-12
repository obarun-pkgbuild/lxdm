# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/community.git/tree/trunk?h=packages/lxdm
# 						Maintainer: Balló György <ballogyor+arch at gmail dot com>
# 						Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# 						Contributor: AndyRTR <andyrtr@archlinux.org>
# 						Contributor: kiefer <jorgelmadrid@gmail.com>

pkgbase=lxdm
pkgname=(lxdm lxdm-gtk3)
pkgver=0.5.3
pkgrel=8
pkgdesc='Lightweight X11 Display Manager'
arch=('x86_64')
url="https://lxde.org/"
license=('GPL')
depends=('gtk2' 'gtk3' 'xorg-server' 'consolekit2')
makedepends=('intltool' 'iso-codes')
optdepends=('iso-codes: show language names in language chooser')
backup=('etc/lxdm/lxdm.conf' 'etc/pam.d/lxdm' 'etc/lxdm/Xsession'
        'etc/lxdm/PreLogin' 'etc/lxdm/LoginReady' 'etc/lxdm/PostLogin'
        'etc/lxdm/PostLogout' 'etc/lxdm/PreReboot' 'etc/lxdm/PreShutdown')
source=(https://downloads.sourceforge.net/lxde/$pkgbase-$pkgver.tar.xz
        set-path.patch
        git-fixes.patch
        default-config.patch
        lxdm.pam
        Xsession)
sha256sums=('4891efee81c72a400cc6703e40aa76f3f3853833d048b72ec805da0f93567f2f'
            '5c8a2470d511e8e5a24961534fc4eca8752e7edb8c7eb4a607d8ac4376dba6c5'
            'ca3b225fb85a4e87ebb48e2b1a20f4bab86bf619a9ad927f08fdc819bc435bb4'
            '89a3fbbab5dcfa110d29d0ea8493d741256ac3ed2c89a19cdb9450c309ae4dbe'
            '7fba72c7f6312c6bfa658a421225c9b2495e697b876ba7c13467fb14a419e50a'
            'f776c245663f28536309f39d88eb10f62ef746343ea13bbd46c3fb6748da5d7f')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare(){
  cd $pkgbase-$pkgver

  # Don't overwrite PATH if already defined
  # https://sourceforge.net/p/lxde/bugs/907/
  patch -Np0 -i ../set-path.patch

  # Apply fixes from git
  patch -Np1 -i ../git-fixes.patch

  # Adjust Arch-specific settings
  patch -Np1 -i ../default-config.patch

  # Use our custom pam and Xsession files
  cp ../lxdm.pam pam/lxdm
  cp ../Xsession data/Xsession

  autoreconf -fi
}

build() {
  # GTK+ 2 version
  [ -d gtk2 ] || cp -r $pkgbase-$pkgver gtk2
  cd gtk2
  ./configure --prefix=/usr --sbindir=/usr/bin --libexecdir=/usr/lib/lxdm \
              --sysconfdir=/etc --localstatedir=/var
  make

  cd "$srcdir"
  # GTK+ 3 version
  [ -d gtk3 ] || cp -r $pkgbase-$pkgver gtk3
  cd gtk3
  ./configure --prefix=/usr --sbindir=/usr/bin --libexecdir=/usr/lib/lxdm \
              --sysconfdir=/etc --localstatedir=/var --enable-gtk3
  make
}

package_lxdm() {
  groups=('lxde')
  depends=('gtk2' 'xorg-server')
  optdepends+=('gnome-themes-standard: default GTK+ theme')

  cd gtk2
  make DESTDIR="$pkgdir" install
  chmod 644 "$pkgdir/etc/lxdm/lxdm.conf"

  # Setup system user and group
  install -dm755 "$pkgdir"/usr/lib/{sysusers,tmpfiles}.d
  echo 'u lxdm - "Lightweight X11 Display Manager" /var/lib/lxdm' > "$pkgdir/usr/lib/sysusers.d/$pkgbase.conf"
  echo 'd /var/lib/lxdm 0700 lxdm lxdm - -' > "$pkgdir/usr/lib/tmpfiles.d/$pkgbase.conf"
}

package_lxdm-gtk3() {
  groups=('lxde-gtk3')
  pkgdesc+=' (GTK+ 3 version)'
  depends=('gtk3' 'xorg-server')
  conflicts=('lxdm')

  cd gtk3
  make DESTDIR="$pkgdir" install
  chmod 644 "$pkgdir/etc/lxdm/lxdm.conf"

  # Setup system user and group
  install -dm755 "$pkgdir"/usr/lib/{sysusers,tmpfiles}.d
  echo 'u lxdm - "Lightweight X11 Display Manager" /var/lib/lxdm' > "$pkgdir/usr/lib/sysusers.d/$pkgbase.conf"
  echo 'd /var/lib/lxdm 0700 lxdm lxdm - -' > "$pkgdir/usr/lib/tmpfiles.d/$pkgbase.conf"
}
