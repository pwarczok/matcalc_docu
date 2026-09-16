# MatCalc Documentation

## Install prerequisites

`pip install -r requirements.txt`

## Serve locally for development

`mkdocs serve`

To develop the documentation locally using the built-in HTTP server of `mkdocs` is recommended.
Just start it with `mkdocs serve`, visit the `localhost` website it tells you and watch it
reload automatically on changes to source files. Source files are located in the `src` folder.

## Publishing

The live site (https://pfandl.github.io/matcalc_docu/) is built and deployed automatically by
GitHub Actions (`.github/workflows/pages.yml`) on every push to `main` - GitHub Pages is
configured to serve from that workflow, not from a committed folder. Just commit and push; it
takes a couple of minutes for changes to go live, and you can watch progress on the repo's
*Actions* tab.

You can still build locally first to check the result:

`mkdocs build`

This writes the site to `docs/`. That folder is no longer what Pages serves, so committing it
is optional - keep it if you find a local copy of the built site useful, drop it if you'd rather
not track generated files.

## Layout-comparison previews

Alongside the live site, four alternative visual directions are built and published to
https://pfandl.github.io/matcalc_docu/preview/ :

| Variant | Config | What it is |
| --- | --- | --- |
| Current | `mkdocs.current.yml` | the live site, unchanged - baseline |
| Handbook | `mkdocs.handbook.yml` | cool, precise, engineering-reference style |
| Companion | `mkdocs.companion.yml` | warm and roomy, for occasional users |
| Atlas | `mkdocs.atlas.yml` | bright and teaching-forward |

All four share `mkdocs.base.yml` (nav, plugins, markdown extensions) via MkDocs' `INHERIT` and
differ only in `theme:` and `extra_css:` (see `src/css/preview-*.css` - first-draft palettes,
not finished designs). The GitHub Actions workflow rebuilds and redeploys all four on every push
to `main`, alongside the live site.

To build them locally instead:

```
pip install -r requirements.txt        # now also installs mkdocs-material
./build-previews.sh
(cd site && python -m http.server)     # -> http://localhost:8000/matcalc_docu/preview/
```

Tutorial 1's screenshots additionally get their red annotation boxes recoloured to match each
direction (magenta / orange / violet). The recoloured PNGs live in `preview-assets/t1/<variant>/`;
regenerate them with `python tools/gen-preview-images.py` if the callout colours change (needs
`pip install pillow numpy scipy` - not part of the site's own requirements, since only that one
script uses them).

These previews exist to compare the four directions on the real site; they are not a finished
redesign of any of them.