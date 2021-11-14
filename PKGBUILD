# Maintainer: Koki Fukuda <ko.fu.dev {a} gmail.com>
pkgname=('mozc' 'ibus-mozc' 'emacs-mozc')
pkgver=2.26.4556.100
pkgrel=1
# Git commit ID
_vc_rev='3dc692847d53e209ef9010791c3ab5ac06fd979b'
arch=('x86_64')
url='https://github.com/google/mozc'
license=('BSD' 'custom')
groups=('mozc-im')
makedepends=('bazel' 'git' 'qt5-base')
source=(
    "mozc::git+https://github.com/google/mozc.git#commit=${_vc_rev}"
    'ibus-include-dir.patch'
    'qt-path.patch'
    'emoji-13-0.tsv'
    'emoji-13-1.tsv'
    'emoji-misc.tsv'
)
sha256sums=(
    'SKIP'
    'SKIP'
    'SKIP'
    'SKIP'
    'SKIP'
    'SKIP'
)

pkgver() {
    grep -E '^(MAJOR|MINOR|BUILD|REVISION) ' "${srcdir}/mozc/src/data/version/mozc_version_template.bzl" |
        sed -E 's/[A-Z]+ *= *([0-9]+)/\1./g' | tr -d '\n' | sed 's/.$//'
}

prepare() {
    cd "${srcdir}/mozc"

    git submodule update --init --recursive

    patch -p1 -i "$srcdir/ibus-include-dir.patch"
    patch -p1 -i "$srcdir/qt-path.patch"

    # Add emoji entries (because upstream doesn't support newer emoji)
    cat "${srcdir}/emoji-13-0.tsv" "${srcdir}/emoji-13-1.tsv" \
        "${srcdir}/emoji-misc.tsv" \
        >>"${srcdir}/mozc/src/data/emoji/emoji_data.tsv"
}

build() {
    cd "${srcdir}/mozc/src"

    bazel build package --config oss_linux -c opt

    # Fill version field for IBus component
    sed -i "s/0.0.0.0/${pkgver}/" "${srcdir}/mozc/src/bazel-bin/unix/ibus/mozc.xml"
}

# check() {
#     cd "${srcdir}/${pkgname}/src"
#     bazel test base:util_test --config oss_linux -c dbg
# }

# Mozc base package
package_mozc() {
    pkgdesc='A Japanese Input Method Editor designed for multi-platform'
    depends=('qt5-base')

    cd "${srcdir}/mozc/src/bazel-bin"
    install -D -m 755 'server/mozc_server' "${pkgdir}/usr/lib/mozc/mozc_server"
    install    -m 755 'gui/tool/mozc_tool' "${pkgdir}/usr/lib/mozc/mozc_tool"

    install -d "${pkgdir}/usr/share/licenses/mozc/"
    cd "${srcdir}/mozc"
    install -m 644 LICENSE src/data/installer/*.html "${pkgdir}/usr/share/licenses/mozc/"
}

# IBus component package
package_ibus-mozc() {
    pkgdesc='IBus engine for Mozc'

    depends=("mozc=${pkgver}" 'ibus>=1.4.1')

    cd "${srcdir}/mozc/src/bazel-bin"
    install -D -m 755 'unix/ibus/ibus_mozc' "${pkgdir}/usr/lib/ibus-mozc/ibus-engine-mozc"
    install -D -m 644 'unix/ibus/mozc.xml' "${pkgdir}/usr/share/ibus/component/mozc.xml"
    install -D -m 755 'renderer/mozc_renderer' "${pkgdir}/usr/lib/mozc/mozc_renderer"

    cd "${srcdir}/mozc/src"
    install -D -m 644 'data/images/unix/ime_product_icon_opensource-32.png' "${pkgdir}/usr/share/ibus-mozc/product_icon.png"
    install    -m 644 'data/images/unix/ui-tool.png'             "${pkgdir}/usr/share/ibus-mozc/tool.png"
    install    -m 644 'data/images/unix/ui-properties.png'       "${pkgdir}/usr/share/ibus-mozc/properties.png"
    install    -m 644 'data/images/unix/ui-dictionary.png'       "${pkgdir}/usr/share/ibus-mozc/dictionary.png"
    install    -m 644 'data/images/unix/48x48/direct.png'        "${pkgdir}/usr/share/ibus-mozc/direct.png"
    install    -m 644 'data/images/unix/48x48/hiragana.png'      "${pkgdir}/usr/share/ibus-mozc/hiragana.png"
    install    -m 644 'data/images/unix/48x48/katakana_half.png' "${pkgdir}/usr/share/ibus-mozc/katakana_half.png"
    install    -m 644 'data/images/unix/48x48/katakana_full.png' "${pkgdir}/usr/share/ibus-mozc/katakana_full.png"
    install    -m 644 'data/images/unix/48x48/alpha_half.png'    "${pkgdir}/usr/share/ibus-mozc/alpha_half.png"
    install    -m 644 'data/images/unix/48x48/alpha_full.png'    "${pkgdir}/usr/share/ibus-mozc/alpha_full.png"
}

# Emacs helper module package
package_emacs-mozc() {
    pkgdesc='Mozc for Emacs'

    depends=("mozc=${pkgver}" 'emacs')
    conflicts=('emacs-mozc-bin')

    cd "${srcdir}/mozc/src/bazel-bin"
    install -D -m 755 'unix/emacs/mozc_emacs_helper' "${pkgdir}/usr/bin/mozc_emacs_helper"
    install -d "${pkgdir}/usr/share/emacs/site-lisp/emacs-mozc/"

    install -m 644 "${srcdir}/mozc/src/unix/emacs/mozc.el" "${pkgdir}/usr/share/emacs/site-lisp/emacs-mozc"
}
