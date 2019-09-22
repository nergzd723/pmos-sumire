# Reference: <https://postmarketos.org/vendorkernel>
# Kernel config based on: arch/arm64/configs/(CHANGEME!)

pkgname="linux-sony-sumire"
pkgver=3.10.84
pkgrel=0
pkgdesc="Sony Xperia Z5 kernel fork"
arch="aarch64"
_carch="arm64"
_flavor="sony-sumire"
url="https://kernel.org"
license="GPL-2.0-only"
options="!strip !check !tracedeps"
makedepends="perl sed installkernel bash gmp-dev bc linux-headers elfutils-dev devicepkg-dev"

# Compiler: latest GCC from Alpine
HOSTCC="${CC:-gcc}"
HOSTCC="${HOSTCC#${CROSS_COMPILE}}"

# Source
_repository="android_kernel_sony_msm8994"
_commit="0befc216a848170b7640c9df975570635b6d1663"
_config="config-${_flavor}.${arch}"
source="
	$pkgname-$_commit.tar.gz::https://github.com/LineageOS/${_repository}/archive/${_commit}.tar.gz
	$_config
"
builddir="$srcdir/${_repository}-${_commit}"

prepare() {
	default_prepare
	downstreamkernel_prepare "$srcdir" "$builddir" "$_config" "$_carch" "$HOSTCC"
}

build() {
	unset LDFLAGS
	make ARCH="$_carch" CC="${CC:-gcc}" \
		KBUILD_BUILD_VERSION="$((pkgrel + 1 ))-postmarketOS"
}

package() {
	# kernel.release
	install -D "$builddir/include/config/kernel.release" \
		"$pkgdir/usr/share/kernel/$_flavor/kernel.release"

	# zImage (find the right one)
	cd "$builddir/arch/$_carch/boot"
	_target="$pkgdir/boot/vmlinuz-$_flavor"
	for _zimg in zImage-dtb Image.gz-dtb *zImage Image; do
		[ -e "$_zimg" ] || continue
		msg "zImage found: $_zimg"
		install -Dm644 "$_zimg" "$_target"
		break
	done
	if ! [ -e "$_target" ]; then
		error "Could not find zImage in $PWD!"
		return 1
	fi
}

sha512sums="5b0f5a9fbef33468baac6fe5f89e2d56d072916f9ac5ed3f63518ca21f2cfc151bf544447ca52cec8f1491fbbaa5fce53cff0cca19d7d8e171bbfddc596efc60  linux-sony-sumire-0befc216a848170b7640c9df975570635b6d1663.tar.gz
2302753d1cc56c57d292f4d623603406d1c6b4b9f50e2e25072361a36ec36fab1760f2d61741af1188b4a8890817ed16a1e02ca7062dbd57c141bca111487d08  config-sony-sumire.aarch64"
