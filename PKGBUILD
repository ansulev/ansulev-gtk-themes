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
# Orchis-Dark-Pro is generated from the installed orchis-theme at build time, so an
# Orchis update reaches it with a rebuild. Two fixes over Arch's orchis-theme 2026_07_07:
#  1. xfwm4: the -xhdpi variant scales its frame XPMs but ships the 36 button SVGs as
#     byte-identical 1x copies (34x36), so on a 2x panel the title bar doubles and the
#     buttons do not. Each <svg> width/height is doubled, viewBox kept: same vector, crisp.
#     The menu dot goes from r=2 to r=4, so it weighs the same as the 10px close/max/min glyphs.
#  2. GTK: checked/indeterminate check + radio use the green success colour #81C995 — they
#     read as "OK", not "on". They take the accent #3281EA instead, which is what upstream's
#     --tweaks primary does; real success states (entry.success, levelbar full) stay green.
#     GTK2 gets the same change on its check/radio PNGs.
makedepends=('orchis-theme' 'perl' 'imagemagick')
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
	_orchis_gtk "$_themesdir/Orchis-Dark-Pro"
	_orchis_xfwm4 "$_themesdir/Orchis-Dark-Pro/xfwm4"
	find "$_themesdir" -type d -exec chmod 755 {} +
	find "$_themesdir" -type f -exec chmod 644 {} +
}

# GTK 2/3/4 from Orchis-Dark-Compact (tighter padding; its xfwm4 is identical to Orchis-Dark's);
# checked check/radio recoloured from success green to accent.
_orchis_gtk() {
	local _src=/usr/share/themes/Orchis-Dark-Compact _dst="$1" _d
	[[ -d "$_src" ]] || { echo "orchis-theme not installed: $_src" >&2; return 1; }
	install -d "$_dst"
	for _d in gtk-2.0 gtk-3.0 gtk-4.0; do cp -a "$_src/$_d" "$_dst/"; done
	sed 's/^Name=.*/Name=Orchis-Dark-Pro/; s/^GtkTheme=.*/GtkTheme=Orchis-Dark-Pro/; s/^MetacityTheme=.*/MetacityTheme=Orchis-Dark-Pro/' \
		"$_src/index.theme" > "$_dst/index.theme"
	# Only rule blocks whose selector holds a checked/indeterminate check or radio.
	perl -0pi -e 's/([^{}]*\b(?:check|radio):(?:hover:)?(?:checked|indeterminate)[^{}]*\{[^}]*?)#81C995/${1}#3281EA/gi' \
		"$_dst"/gtk-3.0/gtk*.css "$_dst"/gtk-4.0/gtk*.css
	if grep -Pzo '(?:check|radio):checked[^{}]*\{[^}]*#81C995' "$_dst"/gtk-3.0/gtk.css >/dev/null; then
		echo "Orchis-Dark-Pro: green checkbox rule left behind" >&2; return 1
	fi
	_orchis_gtk2_assets "$_dst/gtk-2.0/assets"
}

# GTK2 draws check/radio from PNGs (the tick is a transparent cut-out). An HSL shift maps
# #81C995 onto #3281EA (L x0.86, S x2.03, hue +77 deg) and keeps the hover/active/disabled steps.
_orchis_gtk2_assets() {
	local _f _px
	for _f in "$1"/{,menu-}{checkbox,radio}-{checked,mixed}*.png; do
		magick "$_f" -define modulate:colorspace=HSL -modulate 86,203,142.8 "$_f"
	done
	_px=$(magick "$1/checkbox-checked.png" -format '%[hex:p{4,12}]' info:)
	if [[ "$_px" != 3[0-4]8[0-4]E[89A]* ]]; then
		echo "Orchis-Dark-Pro: GTK2 checkbox not recoloured ($_px)" >&2; return 1
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
		echo "Orchis-Dark-Pro: unscaled SVG left behind" >&2; return 1
	fi
	if grep -l 'r="2"' "$_dst"/menu-*.svg >/dev/null; then
		echo "Orchis-Dark-Pro: menu dot not enlarged" >&2; return 1
	fi
}
