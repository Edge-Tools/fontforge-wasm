FROM emscripten/emsdk:5.0.7 AS builder

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential pkg-config gettext bison flex \
        ninja-build python3 python3-pip \
        autoconf automake libtool autotools-dev \
        xz-utils bzip2 ca-certificates \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pip3 install --break-system-packages --no-cache-dir meson==1.7.0 \
 && meson --version

ARG ZLIB_VERSION=1.3.1
ARG ZLIB_SHA256=9a93b2b7dfdac77ceba5a558a580e74667dd6fede4585b91eefb60f03b72df23
ARG LIBFFI_VERSION=3.5.2
ARG LIBFFI_SHA256=f3a3082a23b37c293a4fcd1053147b371f2ff91fa7ea1b2a52e335676bac82dc
ARG PCRE2_VERSION=10.47
ARG PCRE2_SHA256=47fe8c99461250d42f89e6e8fdaeba9da057855d06eb7fc08d9ca03fd08d7bc7
ARG GLIB_VERSION=2.88.1
ARG GLIB_SHA256=51ab804c56f6eab3e5045c774d1290ac5e4c923d4f9a3d8e33123bee45c1840e
ARG LIBPNG_VERSION=1.6.58
ARG LIBPNG_SHA256=8c9b05b675ca7301a458df2c2e46f26e1d41ff36b8863f8c33530bc58c2e6225
ARG LIBJPEG_VERSION=3.1.4.1
ARG LIBJPEG_SHA256=ecae8008e2cc9ade2f2c1bb9d5e6d4fb73e7c433866a056bd82980741571a022
ARG LIBTIFF_VERSION=4.7.1
ARG LIBTIFF_SHA256=f698d94f3103da8ca7438d84e0344e453fe0ba3b7486e04c5bf7a9a3fabe9b69
ARG GIFLIB_VERSION=5.2.2
ARG GIFLIB_SHA256=be7ffbd057cadebe2aa144542fd90c6838c6a083b5e8a9048b8ee3b66b29d5fb
ARG FREETYPE_VERSION=2.14.3
ARG FREETYPE_SHA256=36bc4f1cc413335368ee656c42afca65c5a3987e8768cc28cf11ba775e785a5f
ARG LIBXML2_VERSION=2.13.9
ARG LIBXML2_SHA256=a2c9ae7b770da34860050c309f903221c67830c86e4a7e760692b803df95143a
ARG BROTLI_VERSION=1.1.0
ARG BROTLI_SHA256=e720a6ca29428b803f4ad165371771f5398faba397edf6778837a18599ea13ff
ARG WOFF2_VERSION=1.0.2
ARG WOFF2_SHA256=add272bb09e6384a4833ffca4896350fdb16e0ca22df68c0384773c67a175594
ARG LIBSPIRO_VERSION=20240903
ARG LIBSPIRO_SHA256=1412a21b943c6e1db834ee2d74145aad20b3f62b12152d475613b8241d9cde10

ARG VERSION=20251009
ENV VERSION=${VERSION}
ARG FONTFORGE_SHA256=69046500185a5581b58139dfad30c0b3d8128f00ebbfddc31f2fcf877e329e52

ENV PREFIX=/opt
ENV PKG_CONFIG_PATH=/opt/lib/pkgconfig
ENV EM_PKG_CONFIG_PATH=/opt/lib/pkgconfig
ENV CFLAGS="-O2 -fPIC -pthread -I/opt/include -Wno-error=incompatible-function-pointer-types -Wno-error=incompatible-pointer-types"
ENV CXXFLAGS="${CFLAGS}"
ENV LDFLAGS="-L/opt/lib"
ENV CPPFLAGS="-I/opt/include"

WORKDIR /src
RUN mkdir -p $PREFIX

COPY deps /src/deps
RUN emcc -O2 -fPIC -pthread -c /src/deps/wasm-stubs.c -o /src/wasm-stubs.o \
 && ls -l /src/wasm-stubs.o

ADD https://github.com/madler/zlib/releases/download/v${ZLIB_VERSION}/zlib-${ZLIB_VERSION}.tar.gz zlib.tar.gz
RUN echo "${ZLIB_SHA256}  zlib.tar.gz" | sha256sum -c - \
 && tar -xzf zlib.tar.gz && rm zlib.tar.gz && cd zlib-${ZLIB_VERSION} \
 && emconfigure ./configure --prefix=$PREFIX --static \
 && emmake make -j$(nproc) install

ADD https://github.com/libffi/libffi/releases/download/v${LIBFFI_VERSION}/libffi-${LIBFFI_VERSION}.tar.gz libffi.tar.gz
RUN echo "${LIBFFI_SHA256}  libffi.tar.gz" | sha256sum -c - \
 && tar -xzf libffi.tar.gz && rm libffi.tar.gz && cd libffi-${LIBFFI_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static --disable-docs \
        --disable-multi-os-directory --disable-exec-static-tramp \
 && emmake make -j$(nproc) install

ADD https://github.com/PCRE2Project/pcre2/releases/download/pcre2-${PCRE2_VERSION}/pcre2-${PCRE2_VERSION}.tar.bz2 pcre2.tar.bz2
RUN echo "${PCRE2_SHA256}  pcre2.tar.bz2" | sha256sum -c - \
 && tar -xjf pcre2.tar.bz2 && rm pcre2.tar.bz2 && cd pcre2-${PCRE2_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static \
        --enable-pcre2-8 --disable-pcre2-16 --disable-pcre2-32 \
        --disable-jit --disable-pcre2grep-libz --disable-pcre2grep-libbz2 \
 && emmake make -j$(nproc) install

ADD https://download.gnome.org/sources/glib/2.88/glib-${GLIB_VERSION}.tar.xz glib.tar.xz
RUN echo "${GLIB_SHA256}  glib.tar.xz" | sha256sum -c - \
 && tar -xJf glib.tar.xz && rm glib.tar.xz && cd glib-${GLIB_VERSION} \
 && sed -i "/^network_args = \[ \]\$/{n;s/^if host_system != 'windows'\$/if host_system != 'windows' and host_system != 'emscripten'/}" gio/meson.build \
 && grep -q "host_system != 'emscripten'" gio/meson.build \
 && sed -i "s|#error \"g_unix_fd_query_path() not supported on this platform\"|g_set_error (error, G_FILE_ERROR, G_FILE_ERROR_NOSYS, \"g_unix_fd_query_path() not supported on this platform\"); return NULL;|" glib/glib-unix.c \
 && grep -q 'G_FILE_ERROR_NOSYS, "g_unix_fd_query_path' glib/glib-unix.c \
 && PKG_CONFIG_LIBDIR=$PREFIX/lib/pkgconfig \
    meson setup _build \
        --cross-file /src/deps/emscripten.cross \
        --prefix=$PREFIX --buildtype=release --default-library=static \
        -Dtests=false -Ddocumentation=false -Dman-pages=disabled \
        -Dintrospection=disabled -Dnls=disabled -Dxattr=false \
        -Dselinux=disabled -Dlibmount=disabled -Ddtrace=disabled \
        -Dsystemtap=disabled -Dglib_debug=disabled -Dglib_assert=false \
        -Dglib_checks=false -Dlibelf=disabled -Doss_fuzz=disabled \
        -Dinstalled_tests=false \
 && meson compile -C _build \
 && meson install -C _build \
 && test -f $PREFIX/lib/pkgconfig/gio-2.0.pc \
 && test -f $PREFIX/lib/pkgconfig/gobject-2.0.pc

ADD https://downloads.sourceforge.net/project/libpng/libpng16/${LIBPNG_VERSION}/libpng-${LIBPNG_VERSION}.tar.gz libpng.tar.gz
RUN echo "${LIBPNG_SHA256}  libpng.tar.gz" | sha256sum -c - \
 && tar -xzf libpng.tar.gz && rm libpng.tar.gz && cd libpng-${LIBPNG_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static --disable-tools \
 && emmake make -j$(nproc) install

ADD https://github.com/libjpeg-turbo/libjpeg-turbo/releases/download/${LIBJPEG_VERSION}/libjpeg-turbo-${LIBJPEG_VERSION}.tar.gz libjpeg.tar.gz
RUN echo "${LIBJPEG_SHA256}  libjpeg.tar.gz" | sha256sum -c - \
 && tar -xzf libjpeg.tar.gz && rm libjpeg.tar.gz && cd libjpeg-turbo-${LIBJPEG_VERSION} \
 && emcmake cmake -B build -G Ninja -DCMAKE_INSTALL_PREFIX=$PREFIX -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_FLAGS="$CFLAGS" \
        -DENABLE_SHARED=OFF -DENABLE_STATIC=ON -DWITH_SIMD=OFF -DWITH_TURBOJPEG=OFF \
 && cmake --build build -j$(nproc) --target install

ADD https://download.osgeo.org/libtiff/tiff-${LIBTIFF_VERSION}.tar.gz libtiff.tar.gz
RUN echo "${LIBTIFF_SHA256}  libtiff.tar.gz" | sha256sum -c - \
 && tar -xzf libtiff.tar.gz && rm libtiff.tar.gz && cd tiff-${LIBTIFF_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static --disable-tools --disable-tests \
        --disable-contrib --disable-docs --disable-lzma --disable-zstd --disable-webp \
 && emmake make -j$(nproc) install

ADD https://deb.debian.org/debian/pool/main/g/giflib/giflib_${GIFLIB_VERSION}.orig.tar.gz giflib.tar.gz
RUN echo "${GIFLIB_SHA256}  giflib.tar.gz" | sha256sum -c - \
 && tar -xzf giflib.tar.gz && rm giflib.tar.gz && cd giflib-${GIFLIB_VERSION} \
 && emmake make libgif.a CFLAGS="-std=gnu99 -fPIC -O2 -pthread" \
 && cp libgif.a $PREFIX/lib/ && cp gif_lib.h $PREFIX/include/ \
 && test -f $PREFIX/lib/libgif.a

ADD https://download.savannah.gnu.org/releases/freetype/freetype-${FREETYPE_VERSION}.tar.xz freetype.tar.xz
RUN echo "${FREETYPE_SHA256}  freetype.tar.xz" | sha256sum -c - \
 && tar -xJf freetype.tar.xz && rm freetype.tar.xz && cd freetype-${FREETYPE_VERSION} \
 && emcmake cmake -B build -G Ninja -DCMAKE_INSTALL_PREFIX=$PREFIX -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_FLAGS="$CFLAGS" \
        -DCMAKE_PREFIX_PATH=$PREFIX -DCMAKE_FIND_ROOT_PATH=$PREFIX \
        -DBUILD_SHARED_LIBS=OFF \
        -DFT_DISABLE_HARFBUZZ=ON -DFT_DISABLE_BROTLI=ON -DFT_DISABLE_BZIP2=ON \
        -DFT_REQUIRE_ZLIB=ON -DFT_REQUIRE_PNG=ON \
 && cmake --build build -j$(nproc) --target install

ADD https://download.gnome.org/sources/libxml2/2.13/libxml2-${LIBXML2_VERSION}.tar.xz libxml2.tar.xz
RUN echo "${LIBXML2_SHA256}  libxml2.tar.xz" | sha256sum -c - \
 && tar -xJf libxml2.tar.xz && rm libxml2.tar.xz && cd libxml2-${LIBXML2_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static \
        --without-python --without-lzma --without-modules --without-iconv \
        --with-zlib=$PREFIX \
 && emmake make -j$(nproc) install

ADD https://github.com/google/brotli/archive/refs/tags/v${BROTLI_VERSION}.tar.gz brotli.tar.gz
RUN echo "${BROTLI_SHA256}  brotli.tar.gz" | sha256sum -c - \
 && tar -xzf brotli.tar.gz && rm brotli.tar.gz && cd brotli-${BROTLI_VERSION} \
 && emcmake cmake -B build -G Ninja -DCMAKE_INSTALL_PREFIX=$PREFIX -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_FLAGS="$CFLAGS" -DBUILD_SHARED_LIBS=OFF \
 && cmake --build build -j$(nproc) --target install \
 && test -f $PREFIX/lib/pkgconfig/libbrotlienc.pc

ADD https://github.com/google/woff2/archive/refs/tags/v${WOFF2_VERSION}.tar.gz woff2.tar.gz
RUN echo "${WOFF2_SHA256}  woff2.tar.gz" | sha256sum -c - \
 && tar -xzf woff2.tar.gz && rm woff2.tar.gz && cd woff2-${WOFF2_VERSION} \
 && emcmake cmake -B build -G Ninja -DCMAKE_INSTALL_PREFIX=$PREFIX -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_FLAGS="$CFLAGS" -DCMAKE_CXX_FLAGS="$CXXFLAGS" \
        -DCMAKE_PREFIX_PATH=$PREFIX -DCMAKE_FIND_ROOT_PATH=$PREFIX \
        -DBUILD_SHARED_LIBS=OFF -DCANONICAL_PREFIXES=ON \
 && cmake --build build -j$(nproc) --target woff2common woff2dec woff2enc \
 && cp build/libwoff2common.a build/libwoff2dec.a build/libwoff2enc.a $PREFIX/lib/ \
 && cp build/libwoff2common.pc build/libwoff2dec.pc build/libwoff2enc.pc $PREFIX/lib/pkgconfig/ \
 && cp -r include/woff2 $PREFIX/include/ \
 && test -f $PREFIX/lib/pkgconfig/libwoff2enc.pc && test -f $PREFIX/lib/libwoff2enc.a

ADD https://github.com/fontforge/libspiro/releases/download/${LIBSPIRO_VERSION}/libspiro-dist-${LIBSPIRO_VERSION}.tar.gz libspiro.tar.gz
RUN echo "${LIBSPIRO_SHA256}  libspiro.tar.gz" | sha256sum -c - \
 && tar -xzf libspiro.tar.gz && rm libspiro.tar.gz && cd libspiro-${LIBSPIRO_VERSION} \
 && emconfigure ./configure --host=wasm32-unknown-emscripten --prefix=$PREFIX \
        --disable-shared --enable-static \
 && emmake make -j$(nproc) install \
 && test -f $PREFIX/lib/pkgconfig/libspiro.pc

ADD https://github.com/fontforge/fontforge/releases/download/${VERSION}/fontforge-${VERSION}.tar.xz fontforge.tar.xz
RUN echo "${FONTFORGE_SHA256}  fontforge.tar.xz" | sha256sum -c - \
 && tar -xJf fontforge.tar.xz && rm fontforge.tar.xz

COPY js /src/js

WORKDIR /src/fontforge-${VERSION}

RUN sed -i 's/^add_subdirectory(po)/# &  (removed for headless wasm build)/' CMakeLists.txt \
 && grep -q '# add_subdirectory(po)' CMakeLists.txt \
 && sed -i 's/set_target_properties(fontforgeexe PROPERTIES OUTPUT_NAME "fontforge")/set_target_properties(fontforgeexe PROPERTIES OUTPUT_NAME "fontforge" SUFFIX ".mjs")/' fontforgeexe/CMakeLists.txt \
 && grep -q 'SUFFIX ".mjs"' fontforgeexe/CMakeLists.txt \
 && sed -i 's/-Werror=implicit-function-declaration -Werror=int-conversion //' CMakeLists.txt \
 && ! grep -q -- '-Werror=implicit-function-declaration' CMakeLists.txt

ENV FONTFORGE_LDFLAGS="\
    -L/opt/lib /src/wasm-stubs.o \
    -lpcre2-8 -lwoff2common -lbrotlicommon \
    -lnodefs.js -lworkerfs.js \
    --post-js /src/js/post.js \
    -pthread \
    -sPTHREAD_POOL_SIZE=4 \
    -sPTHREAD_POOL_SIZE_STRICT=0 \
    -sWASM_BIGINT=1 \
    -sALLOW_MEMORY_GROWTH=1 \
    -sALLOW_TABLE_GROWTH=1 \
    -sINITIAL_MEMORY=134217728 \
    -sMAXIMUM_MEMORY=2147483648 \
    -sSTACK_SIZE=8388608 \
    -sEXIT_RUNTIME=0 \
    -sFORCE_FILESYSTEM=1 \
    -sEXPORTED_RUNTIME_METHODS=callMain,FS,NODEFS,WORKERFS,ENV \
    -sINCOMING_MODULE_JS_API=noInitialRun,wasmBinary,locateFile,preRun,postRun,instantiateWasm,print,printErr,arguments,mainScriptUrlOrBlob \
    -sINVOKE_RUN=0 \
    -sMODULARIZE=1 \
    -sEXPORT_ES6=1 \
    -sERROR_ON_UNDEFINED_SYMBOLS=0 \
    -sEMULATE_FUNCTION_POINTER_CASTS=1 \
"

RUN emcmake cmake -B build -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=$PREFIX \
        -DCMAKE_PREFIX_PATH=$PREFIX \
        -DCMAKE_FIND_ROOT_PATH=$PREFIX \
        -DCMAKE_C_FLAGS="$CFLAGS" \
        -DCMAKE_CXX_FLAGS="$CXXFLAGS" \
        -DCMAKE_EXE_LINKER_FLAGS="$FONTFORGE_LDFLAGS" \
        -DBUILD_SHARED_LIBS=OFF \
        -DBUILD_TESTING=OFF \
        -DENABLE_GUI=OFF \
        -DENABLE_NATIVE_SCRIPTING=ON \
        -DENABLE_PYTHON_SCRIPTING=OFF \
        -DENABLE_PYTHON_EXTENSION=OFF \
        -DENABLE_LIBSPIRO=ON \
        -DENABLE_LIBGIF=ON \
        -DENABLE_LIBJPEG=ON \
        -DENABLE_LIBPNG=ON \
        -DENABLE_LIBTIFF=ON \
        -DENABLE_WOFF2=ON \
        -DENABLE_LIBREADLINE=OFF \
        -DENABLE_HARFBUZZ=OFF \
        -DENABLE_DOCS=OFF \
        -DENABLE_FONTFORGE_EXTRAS=OFF \
        -DENABLE_MAINTAINER_TOOLS=OFF \
 && cmake --build build -j$(nproc)

RUN mkdir -p /dist \
 && cp build/bin/fontforge.mjs  /dist/fontforge.mjs \
 && cp build/bin/fontforge.wasm /dist/fontforge.wasm \
 && [ "$(head -c4 /dist/fontforge.wasm | od -An -tx1 | tr -d ' \n')" = "0061736d" ] \
 && [ "$(stat -c%s /dist/fontforge.wasm)" -gt 1048576 ] \
 && ls -lh /dist

FROM scratch AS exportor
COPY --from=builder /dist /dist
