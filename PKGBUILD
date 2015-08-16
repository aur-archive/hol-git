# Maintainer: Nikolaos Bezirgiannis <bezeria@gmail.com>
pkgname=hol-git
pkgver=20140926
pkgrel=2
pkgdesc="Interactive proof assistant for higher order logic [latest Git development version]"
url='http://hol.sourceforge.net/'
arch=('i686' 'x86_64')
license=('BSD')
source=('hol-git::git://github.com/HOL-Theorem-Prover/HOL'
        '0001-fix-holdir.patch'
        )
depends=('mosml')
optdepends=('graphviz')
md5sums=('SKIP'
         'd613c3d825d6f382a24533c0136c5b1e')
conflicts=('hol')
provides=('hol')

pkgver() {
  cd "$srcdir/$pkgname"
  date +%Y%m%d --date="$(git show -s --format=%cD HEAD)"
}

prepare() {
   cd "${srcdir}/${pkgname}"
   patch -p1 -i "${srcdir}/0001-fix-holdir.patch"
}

build() {
  cd "${srcdir}/${pkgname}"
  echo "val mosmldir = \"/usr/bin\";" > config-override
  mosml < tools/smart-configure.sml 
  bin/build
}

package() {
  _oldtop="${srcdir}/${pkgname}"
  _newtop="/opt/hol"

  cd $_oldtop

  # fix broken symlinks
  for _link in $(find ${_oldtop}/sigobj/ -type l)
  do
    _oldsrc=$(readlink -v $_link)
    ln -sfn ${_oldsrc/$_oldtop/$_newtop} $_link
  done

  # fix references inside text files
  sed -i -e 's,'"$_oldtop"','"$_newtop"',g' bin/hol bin/hol.bare bin/hol.bare.noquote bin/hol.noquote sigobj/SRCFILES

  # install hol programs and libraries under /opt/hol
  install -m755 -d "${pkgdir}/opt/${pkgname}"
  install -m644 std.prelude "${pkgdir}/opt/${pkgname}"
  cp -r src "${pkgdir}/opt/${pkgname}"
  cp -r sigobj "${pkgdir}/opt/${pkgname}"
  cp -r bin "${pkgdir}/opt/${pkgname}"
  cp -r tools "${pkgdir}/opt/${pkgname}"

  # install license
  install -m755 -d "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m644 COPYRIGHT "${pkgdir}/usr/share/licenses/${pkgname}/"

  # make symlinks to /usr/bin
  mkdir -p ${pkgdir}/usr/bin
  ln -s /opt/hol/bin/hol ${pkgdir}/usr/bin/hol
  ln -s /opt/hol/bin/hol.noquote ${pkgdir}/usr/bin/hol.noquote
  ln -s /opt/hol/bin/Holmake ${pkgdir}/usr/bin/Holmake

}
