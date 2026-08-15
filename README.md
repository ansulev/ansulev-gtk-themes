# ansulev-gtk-themes

GTK themes for ANSULEV Linux boxes. Two themes that exist in no other repo and
are owned by no package.

| theme | origin |
|---|---|
| `Rosepine-Dark-Moon` | no upstream found — not in archcraft, not in `ansulev/gtk-themes`, not in any Arch or AUR package |
| `Everforest-Dark` | Angel's variant, 75 files apart from the archcraft source |

Both were live-only under `~/.themes` until 2026-08-15, which made them one
`rm -rf` from gone. This repo is now their source of truth.

`OB-Rosepine-Dark-Moon` in `ansulev-openbox-themes` was built from this theme's
own `gtk.css`, so the two stay in step.

## Layout

Each theme is kept **complete** here. The install filter drops what this stack
never loads:

| dropped | why |
|---|---|
| `cinnamon` | not installed |
| `gnome-shell` | not installed |
| `openbox-3` | `ansulev-openbox-themes` owns that layer |

Kept: `gtk-2.0` `gtk-3.0` `gtk-4.0` `xfwm4` `plank` `metacity-1` `index.theme`.
`metacity-1` is deliberate — it costs little and is what a MATE session would
need.

Filtering happens in `package()`, never in the tree. There is no second copy of
these themes to recover from.

## Build

```bash
makepkg -f
makepkg --printsrcinfo > .SRCINFO
```
