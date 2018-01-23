# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/community.git/tree/trunk?h=packages/lxdm
# 						Maintainer: Balló György <ballogyor+arch at gmail dot com>
# 						Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# 						Contributor: AndyRTR <andyrtr@archlinux.org>
# 						Contributor: kiefer <jorgelmadrid@gmail.com>

pkgbase=lxdm
pkgname=(lxdm lxdm-gtk3)
pkgver=0.5.3
pkgrel=6
pkgdesc='Lightweight X11 Display Manager'
arch=('x86_64')
url="https://lxde.org/"
license=('GPL')
depends=('gtk2' 'gtk3' 'xorg-server')
makedepends=('intltool' 'iso-codes')
optdepends=('iso-codes: show language names in language chooser')
backup=('etc/lxdm/lxdm.conf' 'etc/pam.d/lxdm' 'etc/lxdm/Xsession'
        'etc/lxdm/PreLogin' 'etc/lxdm/LoginReady' 'etc/lxdm/PostLogin'
        'etc/lxdm/PostLogout' 'etc/lxdm/PreReboot' 'etc/lxdm/PreShutdown')
source=(https://downloads.sourceforge.net/lxde/$pkgbase-$pkgver.tar.xz
        git-fixes.patch
        default-config.patch
        lxdm.pam
        Xsession)
sha256sums=('4891efee81c72a400cc6703e40aa76f3f3853833d048b72ec805da0f93567f2f'
            'ca3b225fb85a4e87ebb48e2b1a20f4bab86bf619a9ad927f08fdc819bc435bb4'
            '89a3fbbab5dcfa110d29d0ea8493d741256ac3ed2c89a19cdb9450c309ae4dbe'
            '4464cb9a4396d30fad83b0b8ec5d762b8adc7c9f1db4bcc3a78c5d35b8a6d390'
            '3e0477ba2120e46c628dbb4740869971efb332d52ab8dc97fc78eadf14f8d151')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare(){
  cd $pkgbase-$pkgver

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
