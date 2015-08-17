# Maintainer: palmtree5
# Contributor: Simon Peter Nicholls (simon <at> mintsource <dot> org)

pkgname=selinux-refpolicy
_origname=refpolicy
_policyname=refpolicy
pkgver=20140311
pkgrel=3
pkgdesc="Modular SELinux reference policy including headers and docs"
arch=('any')
url="http://oss.tresys.com/projects/refpolicy"
license=('GPL')
groups=('selinux' 'selinux-policies')
depends=(linux-selinux)
makedepends=('selinux-usr-checkpolicy>=2.1.8' 'selinux-usr-policycoreutils>=2.1.10'
             'selinux-usr-libsepol>=2.1.4' 'selinux-usr-libsemanage>=2.0.29' 
             'pyxml')
conflicts=('selinux-refpolicy-arch')
backup=(etc/selinux/config)
options=(!makeflags)
install=${pkgname}.install
source=(http://oss.tresys.com/files/${_origname}/${_origname}-2.${pkgver}.tar.bz2
        config)
sha256sums=('f69437db95548c78a5dec44c236397146b144153149009ea554d2e536e5436f7'
            '3f0d87faaffbae4d8ffde92de6e56309726f53c8a083460c92d2f1d2dccf525d')

build() {
  cd "${srcdir}/${_origname}"
  # Policy build settings
  sed -i -e "s/MONOLITHIC = y/MONOLITHIC = n/" build.conf
  sed -i -e "s/#UNK_PERMS = deny/UNK_PERMS = allow/" build.conf
  sed -i -e "s/DIRECT_INITRC = n/DIRECT_INITRC = y/" build.conf
  sed -i -e "s/UBAC = y/UBAC = n/" build.conf
  # Fix for python2
  sed -i -e "s/python/python2/" Makefile
  make bare
  make conf
  make
}

package(){
  cd "${srcdir}/${_origname}"
  make DESTDIR="${pkgdir}" install
  make DESTDIR="${pkgdir}" install-headers
  make DESTDIR="${pkgdir}" install-docs

  # Create some files and directories necesary for loading policy,
  # which is done via install script.
  install -d -m0755 "${pkgdir}/etc/selinux/${_policyname}/modules"
  install -d -m0700 "${pkgdir}/etc/selinux/${_policyname}/modules/active"
  install -d -m0700 "${pkgdir}/etc/selinux/${_policyname}/modules/active/modules"
  install -d -m0755 "${pkgdir}/etc/selinux/${_policyname}/policy"
  touch "${pkgdir}/etc/selinux/${_policyname}/modules/"{semanage.read.LOCK,semanage.trans.LOCK}
  touch "${pkgdir}/etc/selinux/${_policyname}/policy/policy.28" 
  # Link the policy file for selinux-sysvinit to find it
  cd "${pkgdir}/etc"
  ln -s "selinux/${_policyname}/policy/policy.27" "policy.bin"

  # Install main SELinux config file defaulting to refpolicy
  install -m644 -D "${srcdir}/config" "${pkgdir}/etc/selinux/config"

  # Some changes due to python2
  sed -i -e "s/python/python2/" \
	"${pkgdir}/usr/share/selinux/${_policyname}/include/support/segenxml.py"
  sed -i -e "s/python/python2/" \
	"${pkgdir}/usr/share/selinux/${_policyname}/include/Makefile"
}
