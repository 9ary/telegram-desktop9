# Maintainer: novenary <streetwalkermc@gmail.com>
# Contributor: Sven-Hendrik Haase <svenstaro@gmail.com>
# Contributor: hexchain <i@hexchain.org>
pkgname=telegram-desktop9
pkgver=3.5.0
pkgrel=1
pkgdesc='Official Telegram Desktop client (personal build)'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
depends=('ffmpeg' 'hicolor-icon-theme' 'lz4' 'minizip' 'openal' 'ttf-opensans'
         'qt6-imageformats' 'qt6-svg' 'qt6-wayland' 'qt6-5compat' 'xxhash' 'glibmm'
         'rnnoise' 'pipewire' 'libxtst' 'libxrandr' 'jemalloc' 'abseil-cpp' 'libdispatch')
makedepends=('cmake' 'git' 'ninja' 'python' 'range-v3' 'tl-expected' 'microsoft-gsl' 'meson'
             'extra-cmake-modules' 'wayland-protocols' 'plasma-wayland-protocols' 'libtg_owt')
optdepends=('webkit2gtk: embedded browser features'
            'xdg-desktop-portal: desktop integration')
provides=('telegram-desktop')
conflicts=('telegram-desktop')
source=("https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz"

        "always_delete_for_everyone.patch"
        "always_pin_without_notify.patch"
        "always_send_as_photo_or_album.patch"
        "clicky_sticker_panel.patch"
        "dont_pulse_mentions.patch"
        "use_xdg-open.patch"
        "fix_thread_context_menu.patch"
        "mediaviewer_nofullscreen.patch")
sha512sums=('5a86f8e3dd1b7fca2a615a2de86f9640f14bbf27b7e73f735dad60629ddb99bd5c951d7311f99c044ab6178a49ff997aae5e8da0f8bb6753fa7eecfb12562cef'
            '3a8bff78841a7573a41f74ac913b0c8d711b09356666319efc2959879be832ab64c3c9616b52f6ada7548883ae4200b0747794ebc053ce05b80aadb632230491'
            'ba13a28695902e69901abccc38087b9220e5bb605f1ffb0b13fb77c944ab2b020bfcad10e8138a73ba7643a885640b6e1f86096bb0bad230fe3a484ef42b8869'
            '4da055da633b40b6133d14fd13d1aa9d933b3ba4b19370bc0edbccc02d4e31a9291191f7dc3a2aca9225da8dabca6ed33f90ab757435bebd034b6fed28ac8092'
            '91e6b5af2db63f4c24a43f8e6799be44e27775ef3f0615f760552b1d691b6e734812a87cba9b9753272dc1d3dc5d33ad2890d2dc0be4b9e87680a6dc8a26997c'
            '70be8fc775c58b681e4936f995c71a16a57928862ac42eaf3df04bbdece0e04b865f2b66a830765585d14edb6be269ab883339f984db6ec69b8606d491c3e754'
            '4dfb73add8bf77e520279c13f3fa360429f27505db6bff75dcb4ea9dce08ceac4de491b86b167c0cbf5e9dbbed72494e9ee7539b0c608cdbb3820a5b4c413732'
            '4373ffed3719b67f4936acebadb41947dc013dec162732a00d4508649e554ea96f815f6e5b4b88a2cc74522206ba8b5ed25227f037f034c5b89eefb10fda5bb6'
            'f38793e89a735ce0bc86158d567c156bad83ab5a924898aec6429c61317e64261c533cbe463630815f061c18bf24a378ea0256ed6c28fe968af74f222bdbc546')

prepare() {
    cd tdesktop-$pkgver-full

    local src
    for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"
        [[ $src = *.patch ]] || continue
        msg2 "Applying patch $src..."
        patch -Np1 < "../$src"
    done
}

build() {
    cd tdesktop-$pkgver-full

    export CXXFLAGS+=" -Wp,-U_GLIBCXX_ASSERTIONS"
    # Turns out we're allowed to use the official API key that telegram uses for their snap builds:
    # https://github.com/telegramdesktop/tdesktop/blob/8fab9167beb2407c1153930ed03a4badd0c2b59f/snap/snapcraft.yaml#L87-L88
    # Thanks @primeos!
    cmake \
        -B build \
        -G Ninja \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -DCMAKE_BUILD_TYPE=Release \
        -DTDESKTOP_API_ID=611335 \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c \
        -DDESKTOP_APP_DISABLE_SPELLCHECK=ON
    ninja -C build
}

package() {
    cd tdesktop-$pkgver-full
    DESTDIR="$pkgdir" ninja -C build install
}
