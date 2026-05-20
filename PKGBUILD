pkgname=big-update
pkgver=1.0.2
pkgrel=0
pkgdesc="Update stack for BigLinux with Timeshift, AUR and Flatpak support"
arch=('any')
url="https://github.com/ReinaldoDiasAbreu/big-update"
license=('MIT')

depends=(
    bash
    yay
    flatpak
    timeshift
    libnotify
)

source=("big-update")

sha256sums=('SKIP')

package() {

    install -Dm755 big-update \
        "$pkgdir/usr/bin/big-update"
}
