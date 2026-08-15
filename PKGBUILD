# Maintainer: Angel Sulev <angel@ansulev.com>

# GTK themes that exist nowhere else. Both were live-only in ~/.themes until
# 2026-08-15: owned by no package, absent from archcraft upstream, and absent
# from ansulev/gtk-themes.
#
#   Rosepine-Dark-Moon  no upstream anywhere — the openbox OB-Rosepine-Dark-Moon
#                       was derived from this theme's own gtk.css
#   Everforest-Dark     Angel's variant, 75 files apart from the archcraft source
#                       (cinnamon.css, gnome-shell assets, checkbox/toggle SVGs)
#
# The repo keeps each theme complete; the filter below runs at install time only.
# Pruning the source would be irreversible — there is no other copy.

pkgname=ansulev-gtk-themes
pkgver=1.0
pkgrel=1
pkgdesc="GTK themes — ANSULEV workspace"
arch=('any')
url="https://github.com/ansulev/gtk-themes"
license=('GPL3')
options=(!strip !emptydirs)
optdepends=('xfwm4: xfwm4 window decorations'
            'plank: plank dock theme'
            'marco: metacity-1 decorations, for a MATE session')

# Dropped at install: cinnamon and gnome-shell (neither desktop is installed),
# and openbox-3 (ansulev-openbox-themes owns that layer — Everforest-Dark's copy
# is byte-identical to OB-Everforest-Dark's masks, header aside).
# metacity-1 is kept deliberately against a future MATE switch.
_drop=(cinnamon gnome-shell openbox-3)

package() {
	local _themesdir="$pkgdir"/usr/share/themes _theme _d
	install -d "$_themesdir"
	for _theme in Rosepine-Dark-Moon Everforest-Dark; do
		cp -a "$startdir/$_theme" "$_themesdir/$_theme"
		for _d in "${_drop[@]}"; do
			rm -rf "${_themesdir:?}/$_theme/$_d"
		done
	done
	find "$_themesdir" -type d -exec chmod 755 {} +
	find "$_themesdir" -type f -exec chmod 644 {} +
}
