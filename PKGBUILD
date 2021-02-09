# Maintainer:  Travis Collins <erbrecht at pobox dot com>
pkgname='noisetorch'
pkgver=0.10.0
pkgrel=4
pkgdesc='Real-time microphone noise suppression on Linux.'
arch=('x86_64')
url=https://github.com/lawl/NoiseTorch
license=('GPL3')
depends=('rnnoise' 'pulseaudio' 'polkit')
makedepends=('go' 'cmake')
provides=('noisetorch')
install="${pkgname}.install"
source=("${pkgver}-${pkgrel}.tar.gz::https://github.com/lawl/NoiseTorch/archive/${pkgver}.tar.gz"
        "${pkgname}.install"
        "main.patch"
        "module.patch"
        "version.go")
sha256sums=('f0887a2c8c6cd4c863e4d67da2bcfb2b3900e9f5bfd5b0c1eaef576cc67948c7'
            '171a4179c7e0f3a018a314893e81e598c02cfee274c90d715a302660af920eba'
            '825a8c153178de3731bb4179968207d751bbfa256c0a09ffb4190b53be3fb419'
            '7eda93a24ffb30d631e579c1df5911d388284280f5b32191948928de93c6b04c'
            '1f2f114638a818fe87ad10b9c7eab91f75f75f1d4e49c0a69f989f76a0d9d0a2')

prepare() {
	cd NoiseTorch-${pkgver}
	mkdir -p bin/
	patch -u main.go ../main.patch
	patch -u module.go ../module.patch
	sed "s/VERSIONTOKEN/${pkgver}/" ../version.go > version.go
	export GOPATH="$srcdir/go"
}

build() {
	cd NoiseTorch-${pkgver}/c/ladspa
	make
	cd ${srcdir}/NoiseTorch-${pkgver}
	export CGO_CPPFLAGS="${CPPFLAGS}"
	export CGO_CFLAGS="${CFLAGS}"
	export CGO_CXXFLAGS="${CXXFLAGS}"
	export CGO_LDFLAGS="${LDFLAGS}"
	export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"
	go generate
	go build -o bin/noisetorch
	go clean -modcache
}

package() {
	cd NoiseTorch-${pkgver}
	install -D -m755 bin/noisetorch "${pkgdir}/usr/bin/noisetorch"
	sed -i 's/noisetorch.png/noisetorch/g' "assets/noisetorch.desktop"
	install -D -m644 assets/noisetorch.desktop "${pkgdir}/usr/share/applications/noisetorch.desktop"
	install -D -m644 assets/icon/noisetorch.png "${pkgdir}/usr/share/icons/hicolor/256x256/apps/noisetorch.png"
}
