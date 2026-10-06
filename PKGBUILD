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
# Orchis-Dark-Ansulev is generated from the installed orchis-theme at build time, so an
# Orchis update reaches it with a rebuild. Two fixes over Arch's orchis-theme 2026_07_07:
#  1. xfwm4: the -xhdpi variant scales its frame XPMs but ships the 36 button SVGs as
#     byte-identical 1x copies (34x36), so on a 2x panel the title bar doubles and the
#     buttons do not. Each <svg> width/height is doubled, viewBox kept: same vector, crisp.
#     The menu dot goes from r=2 to r=4, so it weighs the same as the 10px close/max/min glyphs.
#  2. GTK: checked/indeterminate check + radio use the green success colour #81C995 — they
#     read as "OK", not "on". They take the accent #3281EA instead, which is what upstream's
#     --tweaks primary does; real success states (entry.success, levelbar full) stay green.
makedepends=('orchis-theme' 'perl')
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
	_orchis_gtk "$_themesdir/Orchis-Dark-Ansulev"
	_orchis_xfwm4 "$_themesdir/Orchis-Dark-Ansulev/xfwm4"
	find "$_themesdir" -type d -exec chmod 755 {} +
	find "$_themesdir" -type f -exec chmod 644 {} +
}

# GTK 2/3/4 from Orchis-Dark; checked check/radio recoloured from success green to accent.
_orchis_gtk() {
	local _src=/usr/share/themes/Orchis-Dark _dst="$1" _d
	[[ -d "$_src" ]] || { echo "orchis-theme not installed: $_src" >&2; return 1; }
	install -d "$_dst"
	for _d in gtk-2.0 gtk-3.0 gtk-4.0; do cp -a "$_src/$_d" "$_dst/"; done
	sed 's/^Name=.*/Name=Orchis-Dark-Ansulev/; s/^GtkTheme=.*/GtkTheme=Orchis-Dark-Ansulev/; s/^MetacityTheme=.*/MetacityTheme=Orchis-Dark-Ansulev/' \
		"$_src/index.theme" > "$_dst/index.theme"
	# Only rule blocks whose selector holds a checked/indeterminate check or radio.
	perl -0pi -e 's/([^{}]*\b(?:check|radio):(?:hover:)?(?:checked|indeterminate)[^{}]*\{[^}]*?)#81C995/${1}#3281EA/gi' \
		"$_dst"/gtk-3.0/gtk*.css "$_dst"/gtk-4.0/gtk*.css
	if grep -Pzo '(?:check|radio):checked[^{}]*\{[^}]*#81C995' "$_dst"/gtk-3.0/gtk.css >/dev/null; then
		echo "Orchis-Dark-Ansulev: green checkbox rule left behind" >&2; return 1
	fi
}

# xfwm4 from the -xhdpi variant: frame pixmaps as shipped, every SVG declared at 2x,
# and a menu dot twice the radius.
_orchis_xfwm4() {
	local _src=/usr/share/themes/Orchis-Dark-xhdpi/xfwm4 _dst="$1"
	install -d "$_dst"
	cp -a "$_src"/. "$_dst"/
	perl -pi -e 's/(<svg\b[^>]*?)width="([\d.]+)"\s+height="([\d.]+)"/$1 . "width=\"" . ($2*2) . "\" height=\"" . ($3*2) . "\""/e' "$_dst"/*.svg
	sed -i 's/<circle \(cx="272" cy="571.362"\) r="2"/<circle \1 r="4"/' "$_dst"/menu-*.svg
	if grep -l '<svg[^>]*width="34" height="36"' "$_dst"/*.svg >/dev/null; then
		echo "Orchis-Dark-Ansulev: unscaled SVG left behind" >&2; return 1
	fi
	if grep -l 'r="2"' "$_dst"/menu-*.svg >/dev/null; then
		echo "Orchis-Dark-Ansulev: menu dot not enlarged" >&2; return 1
	fi
}
