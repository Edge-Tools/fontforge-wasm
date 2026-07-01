fontforge-wasm — Corresponding Source mirror
==================================================

This repository publishes the Corresponding Source for the WebAssembly
build of fontforge (license: GPL-3.0-or-later) used in edgetools.io.

Contents
  build/      our build recipe: Dockerfile + helper scripts/config/patches.
              Rebuild with:  docker build build/
  upstream/   the exact upstream source archive(s) the build fetched,
              byte-identical and sha256-verified (see below).

Upstream sources:
  zlib.tar.gz
    https://github.com/madler/zlib/releases/download/v1.3.1/zlib-1.3.1.tar.gz
    sha256 9a93b2b7dfdac77ceba5a558a580e74667dd6fede4585b91eefb60f03b72df23
  libffi.tar.gz
    https://github.com/libffi/libffi/releases/download/v3.5.2/libffi-3.5.2.tar.gz
    sha256 f3a3082a23b37c293a4fcd1053147b371f2ff91fa7ea1b2a52e335676bac82dc
  pcre2.tar.bz2
    https://github.com/PCRE2Project/pcre2/releases/download/pcre2-10.47/pcre2-10.47.tar.bz2
    sha256 47fe8c99461250d42f89e6e8fdaeba9da057855d06eb7fc08d9ca03fd08d7bc7
  glib.tar.xz
    https://download.gnome.org/sources/glib/2.88/glib-2.88.1.tar.xz
    sha256 51ab804c56f6eab3e5045c774d1290ac5e4c923d4f9a3d8e33123bee45c1840e
  libpng.tar.gz
    https://downloads.sourceforge.net/project/libpng/libpng16/1.6.58/libpng-1.6.58.tar.gz
    sha256 8c9b05b675ca7301a458df2c2e46f26e1d41ff36b8863f8c33530bc58c2e6225
  libjpeg.tar.gz
    https://github.com/libjpeg-turbo/libjpeg-turbo/releases/download/3.1.4.1/libjpeg-turbo-3.1.4.1.tar.gz
    sha256 ecae8008e2cc9ade2f2c1bb9d5e6d4fb73e7c433866a056bd82980741571a022
  libtiff.tar.gz
    https://download.osgeo.org/libtiff/tiff-4.7.1.tar.gz
    sha256 f698d94f3103da8ca7438d84e0344e453fe0ba3b7486e04c5bf7a9a3fabe9b69
  giflib.tar.gz
    https://deb.debian.org/debian/pool/main/g/giflib/giflib_5.2.2.orig.tar.gz
    sha256 be7ffbd057cadebe2aa144542fd90c6838c6a083b5e8a9048b8ee3b66b29d5fb
  freetype.tar.xz
    https://download.savannah.gnu.org/releases/freetype/freetype-2.14.3.tar.xz
    sha256 36bc4f1cc413335368ee656c42afca65c5a3987e8768cc28cf11ba775e785a5f
  libxml2.tar.xz
    https://download.gnome.org/sources/libxml2/2.13/libxml2-2.13.9.tar.xz
    sha256 a2c9ae7b770da34860050c309f903221c67830c86e4a7e760692b803df95143a
  brotli.tar.gz
    https://github.com/google/brotli/archive/refs/tags/v1.1.0.tar.gz
    sha256 e720a6ca29428b803f4ad165371771f5398faba397edf6778837a18599ea13ff
  woff2.tar.gz
    https://github.com/google/woff2/archive/refs/tags/v1.0.2.tar.gz
    sha256 add272bb09e6384a4833ffca4896350fdb16e0ca22df68c0384773c67a175594
  libspiro.tar.gz
    https://github.com/fontforge/libspiro/releases/download/20240903/libspiro-dist-20240903.tar.gz
    sha256 1412a21b943c6e1db834ee2d74145aad20b3f62b12152d475613b8241d9cde10
  fontforge.tar.xz
    https://github.com/fontforge/fontforge/releases/download/20251009/fontforge-20251009.tar.xz
    sha256 69046500185a5581b58139dfad30c0b3d8128f00ebbfddc31f2fcf877e329e52
