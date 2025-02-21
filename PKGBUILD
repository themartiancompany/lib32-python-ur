# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: Arzet Ro <arzeth0 [at the] gmail [dot] com>
# Contributor: Behnam Momeni <sbmomeni [at the] gmail [dot] com>
# Contributor: Andrew Sun <adsun701@gmail.com>

_tk="false"
_bluez="false"
_ml="lib32-"
_py=python
_Py="Python"
pkgname="${_ml}${_py}"
pkgver=3.13.2
pkgrel=1
_pybasever=3.13
pkgdesc="Next generation of the python high-level scripting language"
arch=(
  'x86_64'
)
license=(
  'custom'
)
url="http://www.${_py}.org"
depends=(
  "${_ml}expat"
  "${_ml}bzip2"
  "${_ml}gdbm"
  "${_ml}openssl"
  "${_ml}libffi"
  "${_ml}zlib"
  "${_ml}libtirpc"
  "${_ml}libnsl"
  "${_ml}readline"
  "${_py}"
)
makedepends=(
  "${_ml}xz"
  "${_ml}sqlite"
  "${_ml}llvm"
  'valgrind'
)
if [[ "${_bluez}" == "true" ]]; then
  makedepends+=(
    "${_ml}bluez-libs"
  )
fi
if [[ "${_tk}" == "true" ]]; then
  makedepends+=(
    "${_ml}tk"
  )
fi
optdepends=(
  "${_ml}sqlite"
  "${_ml}xz: for lzma"
  "${_ml}tk: for tkinter"
)
provides=(
  "${pkgname}3"
)
_http="${url}/ftp"
_ns="${_py}"
_url="${_http}/${_ns}/${pkgver%rc*}"
_tarname="${_Py}-${pkgver}"
source=(
  "${_url}/${_tarname}.tar.xz"{"",".asc"}
  "${_py}-config-32.patch"
)
sha512sums=(
  'bb1c0598914c6d4326554faa568f660f10b20c701d0f36bf1fa58837b6498d728a407416b06ede39604caea1ca93f60545b83b01ae8ee65f55d4cc83242b63fe'
  'SKIP'
  '310345634be6b7b1fc26634e3f27ff5c2fb2a224a7fcd7e03cb5dca7ca1a6422238ee0daf239cd57fceee1d6353de97a5b2bf1938051e6b1b1a4e633913059ad'
)
validpgpkeys=(
  # Ned Deily (Python release signing key) <nad@python.org>
  '0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D'
  # Łukasz Langa (GPG langa.pl) <lukasz@langa.pl>
  'E3FF2839C048B25C084DEBE9B26995E310250568'
  # Pablo Galindo Salgado <pablogsal@gmail.com>
  'A035C8C19219BA821ECEA86B64E628F8D684696D'
  # Thomas Wouters <thomas@python.org>
  '7169605F62C751356D054A26A821E680E5FA6305'
)
prepare() {
  cd \
    "${srcdir}/${_tarname}"
  # Give the configuration script an extention
  patch \
    -Np1 \
    -i \
    "${srcdir}/${_py}-config-32.patch"
  # Fix hard-coded paths
  sed \
    -i \
    "s|base}/lib|base}/lib32|g" \
    "${srcdir}/${_tarname}/Lib/sysconfig/__init__.py"
  sed \
    -i \
    "s|/include|/lib32/python{py_version_short}/include|g" \
    "${srcdir}/${_tarname}/Lib/sysconfig/__init__.py"
  sed \
    -i \
    "s|lib/|lib32/|g" \
    "${srcdir}/${_tarname}/Modules/getpath.c"
  sed \
    -i \
    "s|prefix)/lib|prefix)/lib32|g" \
    "${srcdir}/${_tarname}/Makefile.pre.in"
  # Ensure that we are using the system copy of various libraries (expat, zlib, libffi, and libmpdec),
  # rather than copies shipped in the tarball
  rm \
    -r \
    "Modules/expat"
  # rm \
  #   -rf \
  #     "Modules/_ctypes/"{"darwin","libffi"}*
  # Currently it is impossible to build multilib system mpdecimal module
  # without conflicting header config errors.
  # rm \
  #   -r \
  #   "Modules/_decimal/libmpdec"
}

build() {
  local \
    _configure_opts=()
  _configure_opts+=(
    --prefix="/usr"
    --libdir="/usr/lib32"
    --with-platlibdir="lib32"
    --enable-shared
    --with-threads
    --with-computed-gotos
    --enable-ipv6
    --with-system-expat
    --with-dbmliborder="gdbm:ndbm"
    --enable-loadable-sqlite-extensions
    # Disable bundled pip & setuptools
    --without-ensurepip
    --libexecdir="/usr/lib32"
    --includedir="/usr/lib32/python${_pybasever}/include"
    --exec_prefix="/usr/lib32/python${_pybasever}/"
    --bindir="/usr/bin"
    --sbindir="/usr/sbin"
    --with-suffix='-32'
  )
  cd \
    "${srcdir}/${_tarname}"
  export \
    CC='gcc -m32' \
    CXX='g++ -m32' \
    PKG_CONFIG_PATH='/usr/lib32/pkgconfig' \
    LDFLAGS+=' -m32'
  # No idea why, but when testing in VirtualBox VM,
  # it is `unknown`, so set it to `no`
  export \
    ax_cv_c_float_words_bigendian=no
  # Prefer the built-in libb2
  # in order to avoid accidentally depending on the system lib32-libb2.
  # Also, the support for bundled and external libb2 is removed in the upcoming Python 3.13:
  # https://github.com/python/cpython/commit/325e9b8ef400b86fb077aa40d5cb8cec6e4df7bb
  # Python 3.13 will bundle HACL instead: https://github.com/hacl-star/hacl-star
  export \
    LIBB2_CFLAGS=' ' \
    LIBB2_LIBS=' '
  sed \
    -i \
    's/pkg_cv_LIBB2_CFLAGS="\$LIBB2_CFLAGS"/pkg_failed=yes/' \
    configure
  sed \
    -i \
    's/pkg_cv_LIBB2_LIBS="\$LIBB2_LIBS"/pkg_failed=yes/' \
    configure
  ./configure \
    "${_configure_opts[@]}"
  make \
    EXTRA_CFLAGS="$CFLAGS"
}

package() {
  cd \
    "${srcdir}/${_tarname}"
  # Hack to avoid building again
  sed \
    -i \
    's/^all:.*$/all: build_all/' \
    "Makefile"
  make \
    DESTDIR="${pkgdir}" \
    EXTRA_CFLAGS="$CFLAGS" \
    install
  # some useful "stuff" FS#46146
  install \
    -dm755 \
    "${pkgdir}/usr/lib32/python${_pybasever}/Tools/"{"i18n","scripts"}
  install \
    -m755 \
    "Tools/i18n/"{"msgfmt","pygettext"}".py" \
    "${pkgdir}/usr/lib32/python${_pybasever}/Tools/i18n/"
  install \
    -m755 \
    "Tools/scripts/"{"README",*"py"} \
    "${pkgdir}/usr/lib32/python${_pybasever}/Tools/scripts/"
  # create symlinks
  ln \
    -s \
    "python${_pybasever}-32-config" \
    "${pkgdir}/usr/bin/python3-32-config"
  ln \
    -s \
    "python${_pybasever}-32" \
    "${pkgdir}/usr/bin/python-32"
  ln \
    -s \
    "python${_pybasever}-32-config" \
    "${pkgdir}/usr/bin/python-32-config"
  # remove python3-config, conflicts with regular python
  rm \
    -rf \
    "${pkgdir}/usr/bin/python3-config"
  # License
  install \
    -Dm644 \
    "LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  # Clean up
  # needs bin/ and include/
  rm \
    -rf \
    "${pkgdir}"/{"etc","usr/share"} 
  # Leave the python binary and configure script
  # for dependants to find the headers
  cd \
    "${pkgdir}/usr/bin"
  rm \
    "pydoc"* \
    "idle"*
}
