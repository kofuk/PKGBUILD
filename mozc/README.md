# PKGBUILD for Mozc

This package contains following packages:

- `mozc` (Main Mozc server)
- `ibus-mozc` (IBus frontend for Mozc)
- `emacs-mozc` (Emacs frontend for Mozc)
- `mozc-tool` (Settings GUI)

Although all package are enabled by default, each packages can be enabled separately.
To disable, open `PKGBUILD` and edit `_build_<pkgname>` variables to `no`.
