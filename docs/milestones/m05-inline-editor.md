# M5: Inline editor

## Scope
- `apps/app` `/edit`: renders the user's template with `editable`, `contenteditable` on elements with `data-field`, image replace buttons.
- Plain TS editor script: change tracking, cleaning paste to plain text, "Save", "Publish", "Cancel".
- API: field diff → applied to the data → added to `editedFields` → commit → PR → preview.
- Image upload: type check, resize to ≤2000px, WebP, commit to `assets/`.
- `?edit` on the public site → redirect to the editor.
- Second and third templates in `packages/templates`.

## Definition of done
- [ ] Changing `profile.title` in the editor → a PR that changes only that field → the preview shows the new value.
- [ ] Pasting HTML from another site is saved as plain text; test.
- [ ] Avatar replacement: a 10+ MB file is compressed to the size rule and lands in `assets/`.
- [ ] Only the site owner can edit; test against another user's site.
- [ ] The public site does not load the editor's JS.
- [ ] All templates have the same set of `data-field` per `data-model.md`.
