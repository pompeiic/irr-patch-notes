# IRR Patch Notes

The game fetches `patches.json` from this repo's GitHub Pages URL on reaching the main menu
(`UIRRPatchSubsystem`, configured via `PatchManifestURL` in Project Settings → General Settings).

## Editing

**Graphical editor (preferred):** https://pompeiic.github.io/irr-patch-notes/editor.html — form UI,
live preview matching the in-game rendering, wipe-flag checkboxes, Steam link field. Committing from
the editor needs a fine-grained GitHub token (read/write **Contents** on this repo), pasted top-right
("Remember" stores it in your browser only).

Alternatively edit `patches.json` on github.com (pencil icon) directly. Either way changes are live
within ~1 minute of the Pages deploy. A push with malformed JSON is rejected by the validation workflow.

## Notes formatting

`PatchNotes` supports a markdown subset, rendered identically in the editor preview and in-game
(`UIRRPatchSubsystem::PatchNotesToRichText`): `## ` section header, `- ` bullet, `**bold**`.

## Entry format

| Key | Meaning |
|---|---|
| `Build` | Version this entry belongs to, e.g. `"#1.0.2.0"`. Must match the game's build format. |
| `Date` | ISO 8601, e.g. `"2026-07-27T12:00:00"`. |
| `Title` | Display title, e.g. `"The Safehouse Update"`. |
| `PatchNotes` | The notes text (markdown subset above). `\n` for line breaks. |
| `SteamAnnouncementURL` | Optional link to the Steam announcement for this patch. |
| `bMajorUpdate` | Marks a major update (highlighted in the history menu). |
| `bDelete*SaveGame` | What this patch wipes: All / GameSettings / Mission / World / Vendor / Stash / Inventory / Safehouse. |

## Rules

- **Wipe flags are destructive** — a player updating across several versions gets the **union** of all
  missed patches' flags. Double-check before committing; prefer a PR + review for any wipe.
- Entries with a `Build` newer than the installed game never wipe — they only flip the
  "update available" state. So it's safe to publish notes slightly before the build goes live.
