pkgname=mingw-w64-gtest
pkgver=1.7.0
pkgrel=1
pkgdesc="Google Test - C++ testing utility based on the xUnit framework (like JUnit) (mingw-w64)"
arch=(any)
url="http://code.google.com/p/googletest/"
license=("BSD")
makedepends=(mingw-w64-gcc unzip cmake)
depends=(mingw-w64-crt)
options=(!libtool !strip !buildflags staticlibs)
source=("http://googletest.googlecode.com/files/gtest-$pkgver.zip"
"Toolchain-i686-w64-mingw32.cmake"
"Toolchain-x86_64-w64-mingw32.cmake")
md5sums=('2d6ec8ccdf5c46b05ba54a9fd1d130d7'
         'da10ad00a161800e0b011a77c3efbf76'
         'adea9ccdeea1784c7cd30af195f2a8cb')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build() {
  cd "$srcdir/gtest-$pkgver"
  unset LDFLAGS
  for _arch in ${_architectures}; do
    mkdir "build-${_arch}" && pushd "build-${_arch}"
    PATH="/usr/${_arch}/bin:$PATH" cmake \
      -DCMAKE_INSTALL_PREFIX:PATH=/usr/${_arch} \
      -DCMAKE_INSTALL_LIBDIR:PATH=/usr/${_arch}/lib \
      -DINCLUDE_INSTALL_DIR:PATH=/usr/${_arch}/include \
      -DLIB_INSTALL_DIR:PATH=/usr/${_arch}/lib \
      -DSYSCONF_INSTALL_DIR:PATH=/usr/${_arch}/etc \
      -DSHARE_INSTALL_PREFIX:PATH=/usr/${_arch}/share \
      -DBUILD_SHARED_LIBS:BOOL=ON \
      -DCMAKE_TOOLCHAIN_FILE=${srcdir}/Toolchain-${_arch}.cmake \
      ..
    make
    popd
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname#mingw-w64-}-$pkgver"
    mkdir -p "$pkgdir/usr/$_arch/"{bin,include/gtest/internal,lib}
    install -m644 "include/gtest/"*.h "$pkgdir/usr/$_arch/include/gtest/"
    install -m644 "include/gtest/internal/"*.h "$pkgdir/usr/$_arch/include/gtest/internal/"
    cd "build-$_arch"
    install -m755 libgtest{,_main}.dll "$pkgdir/usr/$_arch/bin/"
    install -m644 libgtest{,_main}.dll.a "$pkgdir/usr/$_arch/lib/"
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
    #rm -r "$pkgdir/usr/${_arch}/share"
  done
}
