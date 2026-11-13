# M7: Chrome extension

## Scope
- `apps/extension`: Manifest V3, Side Panel API, plain TS.
- Auth through an app page, token in `chrome.storage`.
- Side Panel: drafts (state `draft`), copy, edit, "published".
- App API for the extension: list drafts, update state.

## Definition of done
- [ ] The Side Panel opens on any site and shows the current user's drafts.
- [ ] "Copy" puts the text on the clipboard; "published" changes the state in the dashboard.
- [ ] The extension neither reads nor changes page DOMs; manifest permissions are minimal.
- [ ] Signing out of the app invalidates the extension token.
