# Maintainer: Jun <neko-legends>
# Based on Arch Linux's audacity package by David Runge, Christian Heusel
# and Eric Bélanger.

pkgname=omarchy-audacity
_name=Audacity
pkgver=3.7.9
pkgrel=3
pkgdesc='Audacity for Omarchy: move the window by its toolbars, quick start, no splash, network features or startup dialogs'
arch=(x86_64)
url='https://github.com/neko-legends/omarchy-audacity'
license=(GPL-3.0-or-later)
depends=(
  alsa-lib expat flac gdk-pixbuf2 glib2 glibc gtk3 hicolor-icon-theme jack lame
  libgcc libid3tag libogg libsbsms libsndfile libsoxr libstdc++ libvorbis libx11
  lilv mpg123 opus opusfile portaudio portmidi portsmf soundtouch sqlite suil
  twolame util-linux-libs wavpack wxwidgets-common wxwidgets-gtk3 vamp-plugin-sdk
)
makedepends=(
  chrpath cmake ffmpeg gtkmm3 libxkbcommon-x11 lv2 ninja python rapidjson
  vst3sdk xcb-util xcb-util-cursor xcb-util-keysyms
)
optdepends=('ffmpeg: for additional import/export capabilities')
source=(
  "https://github.com/audacity/audacity/archive/refs/tags/$_name-$pkgver.tar.gz"
  0001-move-the-window-by-empty-toolbar-and-menu-bar-space.patch
  0002-start-quickly-without-a-splash-or-probing-every-audio-device.patch
  omarchy-audacity
  omarchy-audacity.desktop
)
sha512sums=('231bc76fd1b0e8dd46f294d1b410ee899bc0c285ee62d61a5c6b381d0568e5d0e567b40ab022678841f957aef9ba6fc690ea56c910b3bf4b533b50344651f9ad'
            'c20d80a8be65331e409cf261599e566431ef8a29c8da329066400a94d196a5b58acee24023edfa86c6574cfe768aa94a7972ba8a13253bd2f41a97554f6cbe3a'
            'ab66a4e73b73f2867188099ee609860a387c0572b433057876b47dd21b526d899e656a0c838065195ffeaacf820f2eda11c6ebb9c625c44740ff8b17315239db'
            '1bfaadf232e52341d0547277be3a144c3ac899e0f343ae22cb6eb82d5b5169f669ebb15e79bf0c4fb625b29de3704857a08882de872af4493a5d4d41b128ec51'
            'c726dd5c0af8b6812f88de30f8921b22f1d84e405e3c51d3056f1e6c7b7f78c4e839415a576923e916c8209044e5ab694aa868121f154e11bd3cc408576281bb')

prepare() {
  cd "audacity-$_name-$pkgver"
  patch --forward --strip=1 --input="$srcdir/0001-move-the-window-by-empty-toolbar-and-menu-bar-space.patch"
  patch --forward --strip=1 --input="$srcdir/0002-start-quickly-without-a-splash-or-probing-every-audio-device.patch"
}

build() {
  # Networking is off, so there is no update check, crash or error reporting,
  # audio.com upload, MuseHub or link handling: Audacity makes no network
  # connections. What's New is off, so nothing pops up at startup.
  local cmake_options=(
    -G Ninja
    -B build
    -D CMAKE_BUILD_TYPE=None
    -D CMAKE_INSTALL_PREFIX=/opt/omarchy-audacity
    -D AUDACITY_BUILD_LEVEL=2
    -D audacity_conan_enabled=OFF
    -D audacity_has_networking=OFF
    -D audacity_has_url_schemes_support=OFF
    -D audacity_has_crashreports=OFF
    -D audacity_has_updates_check=OFF
    -D audacity_has_sentry_reporting=OFF
    -D audacity_has_whats_new=OFF
    -D audacity_has_tests=OFF
    -D audacity_lib_preference=system
    -D audacity_obey_system_dependencies=ON
    -D audacity_use_vst3sdk=system
    -S "audacity-$_name-$pkgver"
    -W no-dev
  )

  export VST3SDK=/usr/src/vst3sdk
  export CFLAGS+=" -DNDEBUG -std=gnu17"
  export CXXFLAGS+=" -DNDEBUG"
  cmake "${cmake_options[@]}"
  cmake --build build
}

package() {
  DESTDIR="$pkgdir" cmake --install build

  # It lives in /opt beside the stock audacity package; only the launcher,
  # its menu entry, icons and project file type go in the system folders.
  local share="$pkgdir/opt/omarchy-audacity/share"
  install -Dm755 omarchy-audacity "$pkgdir/usr/bin/omarchy-audacity"
  install -Dm644 omarchy-audacity.desktop "$pkgdir/usr/share/applications/omarchy-audacity.desktop"
  install -Dm644 "$share/icons/hicolor/scalable/apps/audacity.svg" \
    "$pkgdir/usr/share/icons/hicolor/scalable/apps/omarchy-audacity.svg"
  local size
  for size in 16 22 24 32 48; do
    install -Dm644 "$share/icons/hicolor/${size}x${size}/audacity.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/omarchy-audacity.png"
  done
  install -Dm644 "$share/mime/packages/audacity.xml" "$pkgdir/usr/share/mime/packages/omarchy-audacity.xml"
  rm -rf "$share/applications" "$share/metainfo" "$share/mime"

  install -Dm644 "audacity-$_name-$pkgver/LICENSE.txt" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  install -Dm644 "audacity-$_name-$pkgver/lib-src/libnyquist/nyquist/license.txt" \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE.nyquist"
}
