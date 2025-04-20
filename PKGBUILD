# Maintainer: The Catalyst Convention <your_email@example.com>
# PKGBUILD for Catalyst Browser (Privacy-focused Firefox)

pkgname=catalyst-browser
pkgver=118.0
pkgrel=1
pkgdesc="A privacy-focused web browser based on Firefox, without proprietary software or telemetry."
arch=('x86_64')
url="https://www.mozilla.org/en-US/firefox/"
license=('MPL2')
depends=('gtk3' 'dbus' 'nss' 'libevent' 'sqlite' 'hunspell' 'alsa-lib' 'libxt' 'gdk-pixbuf2')
makedepends=('clang' 'rust' 'autoconf' 'gtk-doc' 'yasm' 'libpulse' 'xz' 'mesa' 'nodejs')
optdepends=('pulseaudio: for audio support')

source=("https://archive.mozilla.org/pub/firefox/releases/$pkgver/source/firefox-$pkgver.source.tar.xz"
        "https://raw.githubusercontent.com/TheCatalystConvention/catalyst-browser-patches/main/disable-telemetry.patch"
        "https://raw.githubusercontent.com/TheCatalystConvention/catalyst-browser-patches/main/remove-proprietary.patch")
sha256sums=('5e37aeb0f9a0b9b52fba5f8f80e9a7fa3a869f82f96b019f206f5b073b99ab0a'  # Firefox source tarball
            'd1f4e9d4303bc38b2e9c0a956a13f58b7e1e927ac7d4f27d4a4e3f3ac2c1614a'  # Disable telemetry patch
            'f7b4a6fbb5ab3a85a0182ea8d39b6a9a7cb249a8fd16f6b98fe1cf8a0f6012c7')  # Remove proprietary patch

prepare() {
    cd "$srcdir/firefox-$pkgver"
    
    # Apply patches for disabling telemetry and removing proprietary code
    patch -p1 < "$srcdir/disable-telemetry.patch"
    patch -p1 < "$srcdir/remove-proprietary.patch"
    
    # Ensure the build is optimized for privacy
    sed -i 's/#define MOZ_TELEMETRY_REPORTING 1/#define MOZ_TELEMETRY_REPORTING 0/' config/autoconf.mk.in
    sed -i 's/#define MOZ_UPDATER 1/#define MOZ_UPDATER 0/' config/autoconf.mk.in
    sed -i 's/#define MOZ_SERVICES_HEALTHREPORT 1/#define MOZ_SERVICES_HEALTHREPORT 0/' config/autoconf.mk.in
}

build() {
    cd "$srcdir/firefox-$pkgver"
    
    # Set the build options (optimized for privacy)
    export MOZCONFIG="$srcdir/mozilla-config"

    # Create a mozconfig file for a clean build
    cat > "$MOZCONFIG" <<EOF
# Disable telemetry
ac_add_options --disable-healthreport
ac_add_options --disable-metrics
ac_add_options --disable-updater

# Disable proprietary code
ac_add_options --disable-pgo
ac_add_options --disable-tests

# Optimizations for user privacy
ac_add_options --enable-application=browser
ac_add_options --enable-optimize
ac_add_options --disable-crashreporter
ac_add_options --disable-default-toolkit

# Disable Firefox DRM
ac_add_options --disable-widevine
EOF

    # Start the build process
    make -j$(nproc)
}

package() {
    cd "$srcdir/firefox-$pkgver"

    # Install the browser
    make install DESTDIR="$pkgdir"
    
    # Clean up unnecessary files
    rm -rf "$pkgdir/usr/lib/firefox/*.debug"
    rm -rf "$pkgdir/usr/share/firefox/*.xpi"
    rm -rf "$pkgdir/usr/share/firefox/browser/omni.ja"
}

# You can run 'makepkg' to build this package.
