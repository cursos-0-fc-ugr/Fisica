# Quizdown extension maintenance

This local extension includes a patched `assets/quizdown.js` to keep KaTeX font loading stable in Quarto preview/render.

## What is patched

- KaTeX `@font-face` references remain as `url(fonts/...)`.
- At runtime, the injected style is rewritten to use the base URL of the loaded `quizdown.js` script.
- The base script matcher is version-agnostic:
  - matches `quarto-contrib/quizdown-x.y.z/quizdown.js`

This avoids page-relative 404 errors like `/Tema2_Dinamica/fonts/...`.

## Version source of truth

- Dependency version is defined once in `quizdown.lua`:
  - `QUIZDOWN_DEP_VERSION`

If you bump this version, Quarto will emit a new folder in `site_libs/quarto-contrib/`.

## Upgrade checklist

1. Replace quizdown assets in `assets/` if needed.
2. Confirm the runtime patch still exists in `assets/quizdown.js`:
   - script lookup uses `document.scripts`
   - matcher includes `quizdown-[0-9]+\.[0-9]+\.[0-9]+/quizdown.js`
   - style rewrite uses `n.replace(/url\(fonts\//g, "url(" + base + "fonts/")`
3. Ensure `fonts/` exists in this extension and is listed in dependency `resources`.
4. Render one document with a quiz block and verify no KaTeX font 404s.

## Notes

- This is a local patch over upstream quizdown 0.6.0 assets.
- Keep this file updated if patch logic changes.
