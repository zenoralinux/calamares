pkgname=calamares
pkgver=3.3.8
pkgrel=1
pkgdesc='Distribution-independent installer framework'
arch=('x86_64')
license=(GPL)
url="https://github.com/calamares/calamares/releases/download"
license=('LGPL')
depends=( 'qt6-svg' 'qt6-webengine' 'yaml-cpp' 'networkmanager' 'upower' 'kcoreaddons' 'kconfig' 'ki18n' 'kservice' \
'kwidgetsaddons' 'kpmcore' 'squashfs-tools' 'rsync' 'boost' 'pybind11' 'cryptsetup' 'doxygen' 'dmidecode' \
'gptfdisk' 'hwinfo' 'kparts' 'polkit-qt6' 'python' 'solid' 'qt6-tools' 'libpwquality' 'ckbcomp' 'qt6-declarative' )
makedepends=('git' 'cmake' 'extra-cmake-modules' 'python-jsonschema' 'python-pyaml' 'python-unidecode' 'gawk')
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source=("$pkgname-$pkgver-$pkgrel.tar.gz::$url/v$pkgver/calamares-$pkgver.tar.gz")
sha256sums=('09435b04e7e5cee16177223068d8a7402c3c6b8126af13ff527af77742c43363')

prepare() {
    cd ${srcdir}/calamares-${pkgver}
    # change version
    _ver="$(cat CMakeLists.txt | grep -m3 -e "  VERSION" | grep -o "[[:digit:]]*" | xargs | sed s'/ /./g')"
    _ver="$pkgver"
    printf 'Version: %s-%s\n' "${_ver}" "${pkgrel}"
    sed -i -e "s|\${CALAMARES_VERSION_MAJOR}.\${CALAMARES_VERSION_MINOR}.\${CALAMARES_VERSION_PATCH}|${_ver}-${pkgrel}|g" CMakeLists.txt
    sed -i -e "s|CALAMARES_VERSION_RC 1|CALAMARES_VERSION_RC 0|g" CMakeLists.txt

	# modify desktop file
	sed -i -e 's#Exec=sh.*#Exec= pkexec "calamares"#g' "$srcdir/${pkgname}-${pkgver}/calamares.desktop"
	sed -i -e 's#Name=.*#Name=Install Zenora Linux#g' "$srcdir/${pkgname}-${pkgver}/calamares.desktop"
	sed -i -e 's#GenericName=.*#GenericName=Zenora Linux Installer#g' "$srcdir/${pkgname}-${pkgver}/calamares.desktop"
	sed -i -e 's#Comment=.*#Comment=Zenora Linux Installer#g' "$srcdir/${pkgname}-${pkgver}/calamares.desktop"


	# patches here

}

build() {
    cd ${srcdir}/calamares-${pkgver}

    _cpuCount=$(grep -c -w ^processor /proc/cpuinfo)

    export CXXFLAGS+=" -fPIC"

    cmake -S . -Bbuild \
        -GNinja \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib \
        -DWITH_APPSTREAM=OFF \
        -DWITH_PYBIND11=OFF \
        -DWITH_QT6=ON \
        -DSKIP_MODULES="dracut dracutlukscfg \
        dummycpp dummyprocess dummypython dummypythonqt \
		license notesqml \
        openrcdmcryptcfg fsresizer \
        rawfs mkinitfs contextualprocess interactiveterminal \
        plymouthcfg plasmalnf services-openrc \
		tracking webview"

    cmake --build build --parallel $_cpuCount
}

package() {
	cd ${srcdir}/calamares-${pkgver}/build
    DESTDIR="${pkgdir}" cmake --build . --target install
}
