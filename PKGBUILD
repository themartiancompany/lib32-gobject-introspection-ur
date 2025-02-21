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
# Maintainer: Behnam Momeni <sbmomeni [at the] gmail [dot] com>
# Contributor: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>

_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "GNU/Linux" ]]; then
  _libc="glibc"
elif [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot"
fi
# I'm not sure if it's needed
# this days but it seems looked for
_bison="true"
_docs="false"
_tests="false"
_ml="lib32-"
_py="python"
_pyver_autodetect="false"
_pymajver=3.13
_py_found="$( \
  command \
    -v \
    "${_py}${_pymajver}-32")"
if [[ "${_py_found}" == "" ]]; then
  _pymajver_autodetect="true"
fi
if [[ "${_pymajver_autodetect}" == "true" ]]; then
  _pyver="$( \
    "${_py}-32" \
      -V | \
      awk '{print $2}')"
  _pymajver="${_pyver%.*}"
fi
_proj="gnome"
_pkg=gobject-introspection
pkgbase="${_ml}${_pkg}"
_pkgbase=gobject-introspection
pkgname=(
  "${pkgbase}"
  "${pkgbase}-runtime"
  "${_ml}libgirepository"
)
pkgver=1.82.0
_commit="e30c00acab2d7216e742d602988c935f787755d4"
pkgrel=2
pkgdesc="Introspection system for GObject-based libraries (32-bit)"
url="https://wiki.${_proj}.org/Projects/GObjectIntrospection"
arch=(
  "x86_64"
)
license=(
  "LGPL"
  "GPL"
)
_glib_ver="2.82.4"
_glib_commit="ca20e4ac71864f08e980dc044ac96c06d5482b37"
makedepends=(
  "${_ml}cairo"
  "${_ml}${_libc}"
  "${_bl}libffi"
  "${_py}"
  "${_py}-markdown"
  "${_py}-setuptools"
  "${_py}-sphinx"
  "meson"
  "${_ml}glib2=${_glib_ver}"
)
if [[ "${_bison}" == "true" ]]; then
  makedepends+=(
    "bison"
  )
fi
if [[ "${_docs}" == "true" ]]; then
  makedepends+=(
    "gtk-doc"
  )
fi
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    "git"
  )
fi
_http="https://gitlab.${_proj}.org"
_ns="GNOME"
_url="${_http}/${_ns}/${_pkg}"
_tag_name="commit"
_tag="${_commit}"
source=(
  "git+${_url}.git#${_tag_name}=${_tag}"
  "git+${_http}/${_ns}/glib.git#${_tag_name}=${_glib_commit}"
  '0001-scanner-Ignore-_Complex.patch'
  "x86-linux-gnu"
)
sha512sums=(
  'SKIP'
  'SKIP'
  'a2d754b1d259134a9c2ce061f4b1f6174b65cef94d703ba59d601ed8a3af9014ce20307c74a8265fa4a4427178483af137391bcb5d9052a0339ff9b2f18e46d0'
  '0be6a1cb2b7d82b25aabdcb7893e67d003d132471055d5853f5b3139c6665b3a1d6b65c63b46388b4af9c2fa0b2200791fb2f727c066e3a89d2c44be569104ad'
)
validpgpkeys=(
  # Philip Withnall <philip@tecnocode.co.uk>
  '923B7025EE03C1C59F42684CF0942E894B2EAFA0'
  # Marco Trevisan <marco@trevi.me>
  'D4C501DA48EB797A081750939449C2F50996635F'
) 

pkgver() {
  cd \
    "${_pkg}"
  git \
    describe \
      --tags | \
    sed \
      's/[^-]*-g/r&/;s/-/+/g'
}

prepare() {
  cd \
    "${_pkg}"
  # Unbreak WebKitGTK build
  # https://gitlab.gnome.org/GNOME/gobject-introspection/-/issues/519
  git \
    apply \
      -3 \
      "../0001-scanner-Ignore-_Complex.patch"
  if [[ "${_tests}" == "true" ]]; then
    git \
      submodule \
        init
    git \
      submodule \
        set-url \
	  "${_pkg}-tests" \
	  "$srcdir/${_pkg}-tests"
    git \
      -c \
        protocol.file.allow="always" \
      -c protocol.allow="never" \
      submodule \
        update
  fi
}

build() {
  local \
    _meson_opts=()
  _meson_opts=(
    --prefix="/usr"
    --sbindir="bin"
    --buildtype="plain"
    --libexecdir="lib32"
    --libdir="/usr/lib32"
    --auto-features="enabled"
    --wrap-mode="nodownload"
    -D
      gir_dir_prefix="lib32/gobject-introspection/share"
    -D
      gtk_doc="${_docs}"
    -D
      python="python${_pymajver}-32"
    -D
      glib_src_dir="${srcdir}/glib"
    --cross-file="x86-linux-gnu"
  )
  export \
    CC="gcc -m32" \
    CXX="g++ -m32" \
    CFLAGS+=" -Wno-incompatible-pointer-types"
  meson \
    setup \
      "${_pkg}" \
      "build" \
      "${_meson_opts[@]}"
  sed \
    -i \
      's/env python3$/env python3-32/' \
      "build/tools/g-ir-"{"scanner","doc-tool","annotation-tool"}
  meson \
    compile \
      -C \
        "build"
}

check() {
  meson \
    test \
      -C \
        "build" \
      --print-errorlogs
}

_pick() {
  local \
    _p="${1}" \
    _f \
    _d
  shift
  for _f; do
    _d="${srcdir}/${_p}/${_f#$pkgdir/}"
    mkdir \
      -p \
      "$(dirname \
           "${_d}")"
    mv \
      "${_f}" \
      "${_d}"
    rmdir \
      -p \
      --ignore-fail-on-non-empty \
      "$(dirname \
           "${_f}")"
  done
}

package_lib32-gobject-introspection() {
  local \
    _f
  depends+=(
    "gobject-introspection>=${pkgver}"
    "${_ml}gobject-introspection-runtime=${pkgver}-${pkgrel}"
    "${_ml}libgirepository=${pkgver}-${pkgrel}"
    "${_ml}glib2"
    "${_ml}${_libc}"
    "${_ml}libffi"
    "${_ml}${_py}>=${_pymajver}"
    "${_py}-mako"
    "${_py}-markdown"
    "${_py}-setuptools"
  )
  export \
    CC="gcc -m32" \
    CXX="g++ -m32"
  meson \
    install \
      -C \
        build \
      --destdir \
        "${pkgdir}"
  cd \
    "${pkgdir}"
  "${_py}-32" \
    -m \
      "compileall" \
      -d \
        "/usr/lib32/${_pkg}" \
      "usr/lib32/${_pkg}"
  "${_py}-32" \
    -O \
    -m \
      "compileall" \
      -d \
        "/usr/lib32/${_pkg}" \
      "usr/lib32/${_pkg}"
  _pick \
    "libg" \
    "usr/lib32/libgirepository-1.0.so"*
  _pick \
    "libg" \
    "usr/lib32/pkgconfig/gobject-introspection"*"-1.0.pc"
  _pick \
    "libg" \
    "usr/lib32/girepository-1.0/GIRepository-2.0.typelib"
  _pick \
    "runtime" \
    "usr/lib32/girepository-1.0"
  rm \
    -r \
    "usr/"{"include","share"}
  for _f in "usr/bin/"*; do
    mv \
      "${_f}" \
      "${_f}-32"
  done
}

package_lib32-gobject-introspection-runtime() {
  pkgdesc+=" (runtime library)"
  depends=(
    "${_ml}libgirepository=${pkgver}-${pkgrel}"
  )
  mv \
    "runtime/"* \
    "${pkgdir}"
}

package_lib32-libgirepository() {
  pkgdesc+=" - runtime library"
  depends=(
    "${_ml}glib2"
    "${_ml}${_libc}"
    "${_ml}libffi"
    "libffi.so"
    "libg"{"lib","object","module"}"-2.0.so"
  )
  provides=(
    "libgirepository-1.0.so"
  )
  mv \
    "libg/"* \
    "${pkgdir}"
}
