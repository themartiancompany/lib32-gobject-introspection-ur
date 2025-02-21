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

_ml="lib32-"
_py="python"
_proj="gnome"
_pkg=gobject-introspection
pkgbase="${_ml}${_pkg}"
_pkgbase=gobject-introspection
pkgname=(
  "${pkgbase}"
  "${pkgbase}-runtime"
)
pkgver=1.82.0
_commit="4a365a3137afd51cfaaa39f7784ecb822978e85f"
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
_glib_commit="e35c796e62eddc827112e692defc0a164042b0ef"
_pyver=3.11
depends=(
  "${_py}-mako"
  "${_py}-markdown"
  "lib32-${_py}>=${_pyver}"
)
makedepends=(
  "bison"
  "${_ml}cairo"
  "git"
  "gtk-doc"
  "${_py}-sphinx"
  "meson"
  "${_ml}glib2=${_glib_ver}"
)
_http="https://gitlab.${_proj}.org"
_ns="GNOME"
_url="${_http}/${_ns}/${_pkg}"
_tag_name="commit"
_tag="${_commit}"
source=(
  "git+${_url}.git#${_tag_name}=${_tag}"
  "git+${_http}/${_ns}/glib.git#${_tag_name}=${_glib_commit}"
  "x86-linux-gnu")
sha512sums=(
  'SKIP'
  'SKIP'
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
      gtk_doc="false"
    -D
      python="python${_pyver}-32"
    -D
      glib_src_dir="${srcdir}/glib"
    --cross-file="x86-linux-gnu"
  )
  export \
    CC="gcc -m32" \
    CXX="g++ -m32"
  "/usr/bin/meson" \
    setup \
      "${_pkg}" \
      "build" \
      "${_meson_opts[@]}"
  sed \
    -i \
      's/env python3$/env python3-32/' \
      "build/tools/g-ir-"{"scanner","doc-tool","annotation-tool"}
  "/usr/bin/meson" \
    compile \
      -C \
        "build"
}

check() {
  "/usr/bin/meson" \
    test \
      -C \
        "build"
}

_pick() {
  local p="$1" f d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir -p "$(dirname "$d")"
    mv "$f" "$d"
    rmdir -p --ignore-fail-on-non-empty "$(dirname "$f")"
  done
}

package_lib32-gobject-introspection() {
  depends+=(
    "gobject-introspection>=${pkgver}"
    "${_ml}gobject-introspection-runtime=${pkgver}-${pkgrel}"
  )
  export \
    CC="gcc -m32" \
    CXX="g++ -m32"
  "/usr/bin/meson" \
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
    "runtime" \
    "usr/lib32/lib"*
  _pick \
    "runtime" \
    "usr/lib32/girepository-"*
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
    "${_ml}glib2"
  )
  provides=(
    "libgirepository-1.0.so"
  )
  mv \
    "runtime/"* \
    "${pkgdir}"
}
