
# firefox-tui

userChrome / userContent overlay that frames Firefox toolbars as labeled panels (`tabs`, `nav`, `bookmarks`, `sidebar`, `main`). Monospace UI, square controls, caption buttons (`− □ ×`) packed into the tabs strip instead of the native titlebar gap.

Does not theme web content. Use a userstyle extension for sites.

## Requirements

- Firefox with `toolkit.legacyUserProfileCustomizations.stylesheets` enabled
- Write access to the profile directory (`about:support` → Profile Folder)
- Optional: `DM Mono`, `JetBrains Mono`, or `Cascadia Mono` installed (Consolas is the fallback)

Verified against Firefox 154. Toolbar IDs shift across major UI refactors; treat that as a patch cycle, not a support bug.

## Install

1. `about:config` → `toolkit.legacyUserProfileCustomizations.stylesheets` → `true`
2. `about:support` → Open Profile Folder
3. Create `chrome` if missing
4. Copy:

```
<profile>/chrome/userChrome.css
<profile>/chrome/userContent.css
```

5. Quit the Firefox process entirely and relaunch. userChrome is not hot-reloaded.

Bookmarks frame is visible only when the Bookmarks Toolbar is shown (`View → Toolbars → Bookmarks Toolbar → Always Show`). Sidebar frame appears when a sidebar is open (`Ctrl+B`).

## Files

| Path | Scope |
| --- | --- |
| `chrome/userChrome.css` | Window chrome: toolbars, tabs, url bar, sidebar, caption buttons |
| `chrome/userContent.css` | Privileged `about:*` documents only |

`userContent.css` does not restyle `about:newtab` search UI reliably. Parts of that document are isolated from stylesheet injection.

## Layout

`#TabsToolbar` is the `tabs` frame and contains both the tab strip and `.titlebar-buttonbox-container`. Native titlebar spacers are removed so tabs stay left-aligned.

Vertical rhythm is `--tui-gap` between frames and `--tui-pad` from the window edge. Labels are `::before` on the frame, parked on the top border (`top: -7px`). Parent overflow is forced visible so those labels are not clipped.

## Configuration

All tokens sit in `:root` of `userChrome.css`:

```css
--tui-bg        /* chrome around frames */
--tui-bg-2      /* frame fill */
--tui-bg-3      /* selected tab / url field */
--tui-fg
--tui-muted     /* labels */
--tui-border
--tui-accent
--tui-danger    /* close-button hover */
--tui-font
--tui-pad       /* window inset */
--tui-gap       /* inter-frame spacing */
--tui-label-size
```

Change tokens. Do not scatter hex through the rule blocks.

## Failure modes

- Flag off → files load as inert text, UI unchanged
- Wrong profile directory → same
- Restart skipped → same
- Caption buttons clipped → raise `#TabsToolbar` `min-height`
- Labels clipped → increase `--tui-gap` before moving `top`
- Post-update drift → re-check `#TabsToolbar`, `#nav-bar`, `#PersonalToolbar`, `#tabbrowser-tabbox`, `.titlebar-buttonbox-container` in Browser Toolbox (`Ctrl+Alt+Shift+I`)

## License

MIT
