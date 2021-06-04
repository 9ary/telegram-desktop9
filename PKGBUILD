# Maintainer: novenary <streetwalkermc@gmail.com>
# Contributor: hexchain <i@hexchain.org>
pkgname=telegram-desktop9
pkgver=2.7.4
pkgrel=1
pkgdesc='Official Telegram Desktop client (personal build)'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
depends=('ffmpeg' 'hicolor-icon-theme' 'lz4' 'minizip' 'openal' 'ttf-opensans'
         'qt5-imageformats' 'xxhash' 'libdbusmenu-qt5' 'kwayland' 'gtk3' 'glibmm' 'webkit2gtk')
makedepends=('cmake' 'git' 'ninja' 'python' 'range-v3' 'tl-expected' 'microsoft-gsl' 'libtg_owt')
provides=('telegram-desktop')
conflicts=('telegram-desktop')
source=("https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz"
        "71deaa48afab2bcf9df67b9b347b1f44aad3a9ce._patch::https://github.com/telegramdesktop/tdesktop/commit/71deaa48afab2bcf9df67b9b347b1f44aad3a9ce.patch"
        "a8807bc915f2439acc7c84f06d931d96d6ca602a._patch::https://github.com/telegramdesktop/tdesktop/commit/a8807bc915f2439acc7c84f06d931d96d6ca602a.patch"
        "fix-webview-includes._patch::https://raw.githubusercontent.com/archlinux/svntogit-community/bf53e23b5321cbc664bfb93e4350b179b26238f6/trunk/fix-webview-includes.patch"

        "always_delete_for_everyone.patch"
        "always_pin_without_notify.patch"
        "always_send_as_photo_or_album.patch"
        "clicky_sticker_panel.patch"
        "dont_pulse_mentions.patch"
        "no_circles.patch"
        "use_xdg-open.patch"
        "fix_thread_context_menu.patch"
        "mediaviewer_nofullscreen.patch")
sha512sums=('0a796d7a8c5e5982bc60f19c41da53996a609bf794fad224e7beea5fc3816b5cf35f16b0ec2cc7279085c69996063a44085f48e1596dfe746d260a2e8f1b2d14'
            'cabdadddc7a6873c7c6fc8128ceddf2712c0c73d3316520482162e5c67937bda2a627ddb2f078558a52e812c2a876351196488a5a75381d84a43815b873bac5b'
            '72ef9bddef1f40e09b9c4334153f1ab8aaddf63d95d9f25dc1b0a0826b7f2ae24f7c5fc4cb5ce90aae2c3144a5d52c4cb870d643f447d654190c4a2c78079354'
            '5492c73f0b984da1e2d1f21c3a36c11c4b9ad511522dccd4d6440681f68d6ebc9e672806a534b1e551f736f080d3ef307c8ddd012e4646bd84d09c5e8fa85a40'
            'fdef3a430bdd60d88c9e9011ee878805e7803699204a2a7e22797d0f8729bf7dc0543851083ad700a4ece32bc768b6bfeb6f0135c8c039e035b22afb6df1171d'
            'dc5ffda130496c44bfe52792e856dac811b1a8e48b463529dd54396ad1b45915f8b6d9fcb6cb254f9350b3440d7b94a67d1c19660962f0350015061b021af6f1'
            '4da055da633b40b6133d14fd13d1aa9d933b3ba4b19370bc0edbccc02d4e31a9291191f7dc3a2aca9225da8dabca6ed33f90ab757435bebd034b6fed28ac8092'
            '19a13dbe8d8af5400bb64c2004fbf31b162da7ecc46636b5e5d93dc860ab0a6b9d81331789d11171451ade679459e0338134b64a21cc69b01ab201d64ec7560e'
            '673e2a28781d0d604549c621592b1017ad306ddaf6d1beedfe73f3f1357fbb6afd994a324dfa15029789bdf8a4d6e85ad12a3877519618f6585bbc1927c06900'
            '8ec6b1739a1391b75a2653fff704a7d22e830c526acffe138936bbd20047bd24831e42558fa22069d7e914e762bbcfa2e1b14a8fd3911fd8bbbd0662d8baac14'
            'fd8d0bf64e1e682711f161b1f22ada8ab7551e6bbd794aa3bc53cdfecc0d60c688c1b82525d87ae26c6eb7f9b958c836a0ed57851ef95d32d031921a973e0675'
            'e6a10c1304e01676373c77d27629d93c085fa4e34e80ce1e4bd10af9cfb0a24c1fe2077cc0fdda83162e865cfab6811c9bc27aa13661c6d300c54749ffaef796'
            'e74c9c4cef3c6ac17c1cac19ecc4fc788b20a06776344a32f5ebdeeab9064c61a5ee302eca1f3d25af63f1e4c11b5ee615f3afdfefa30c1b1626fb093bf26866')

prepare() {
    cd "$srcdir/tdesktop-$pkgver-full"

    local src
    for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"
        [[ $src = *.patch ]] || continue
        msg2 "Applying patch $src..."
        patch -Np1 < "../$src"
    done

    cd cmake
    # force webrtc link to libjpeg
    echo "target_link_libraries(external_webrtc INTERFACE jpeg)" | tee -a external/webrtc/CMakeLists.txt
    cd ../Telegram/lib_webview
    patch -Np1 -i "$srcdir"/fix-webview-includes._patch

    cd ..
    patch -Np2 -i "$srcdir"/71deaa48afab2bcf9df67b9b347b1f44aad3a9ce._patch
    patch -Np2 -i "$srcdir"/a8807bc915f2439acc7c84f06d931d96d6ca602a._patch
}

build() {
    cd tdesktop-$pkgver-full

    # Turns out we're allowed to use the official API key that telegram uses for their snap builds:
    # https://github.com/telegramdesktop/tdesktop/blob/8fab9167beb2407c1153930ed03a4badd0c2b59f/snap/snapcraft.yaml#L87-L88
    # Thanks @primeos!
    cmake . \
        -B build \
        -G Ninja \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -DCMAKE_BUILD_TYPE=Release \
        -DTDESKTOP_API_ID=611335 \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c \
        -DTDESKTOP_LAUNCHER_BASENAME="telegramdesktop" \
        -DDESKTOP_APP_SPECIAL_TARGET="" \
        -DDESKTOP_APP_DISABLE_SPELLCHECK=ON
    ninja -C build
}

package() {
    cd tdesktop-$pkgver-full
    DESTDIR=$pkgdir ninja -C build install
}
