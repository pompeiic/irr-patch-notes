# Incursion Red River — News & Patch Notes

The game fetches two files from this repo's GitHub Pages URL at startup (`UIRRPatchSubsystem`,
configured via `PatchManifestURL` / `NewsManifestURL` in Project Settings → **Patch System**,
`IRRPatchSystem` module):

- **`patches.json`** — patch notes + save-wipe flags, shown as the post-update popup.
- **`news.json`** — general news (events, announcements). Each entry is delivered **once** into every
  player's in-game inbox as a message (`UIRRInboxComponent` news sync).

## Editing

**Graphical editor (preferred):** https://pompeiic.github.io/irr-patch-notes/editor.html — switch
between **Patch notes** and **News** with the tabs top-left; form UI, live preview matching the
in-game rendering, wipe-flag checkboxes, Steam/link fields. Committing from
the editor needs a fine-grained GitHub token (read/write **Contents** on this repo), pasted top-right
("Remember" stores it in your browser only).

Alternatively edit `patches.json` / `news.json` on github.com (pencil icon) directly. Either way changes
are live within ~1 minute of the Pages deploy. A push with malformed JSON is rejected by the validation
workflow.

## One-time setup: your editor token

The editor's **Commit to GitHub** button needs a personal access token — a per-person key that lets the
page commit as you. It is stored **only in your browser** (never in the repo) and can touch **only this
repo's files**, nothing else you have access to.

1. Make sure you're a **collaborator** on this repo (ask the repo owner to invite you:
   repo → Settings → Collaborators) and have accepted the invite.
2. On github.com: click your avatar (top right) → **Settings** → **Developer settings** (bottom of the
   left sidebar) → **Personal access tokens** → **Tokens (classic)** → **Generate new token (classic)**.
3. Fill in:
   - **Note:** `patch-notes-editor`
   - **Expiration:** 1 year (you'll regenerate it after that)
   - **Scopes:** tick **`public_repo`** only — leave everything else unticked
4. Click **Generate token** and copy the `ghp_…` string (it's shown only once).
5. Open the [editor](https://pompeiic.github.io/irr-patch-notes/editor.html), paste the token into the
   field at the top right, click **Remember**.

Done — Commit to GitHub now works, and your commits show up under your own name. If you switch
browser/PC or the token expires, repeat steps 2–5.

> **Why a classic token?** Fine-grained tokens can only target repos *you own*, so they don't work for
> collaborators on this personal repo. The repo **owner** can use a fine-grained token instead
> (Repository access: *Only select repositories* → this repo; Permissions → Contents: *Read and write*)
> for tighter scoping.

**Troubleshooting — `403 Resource not accessible by personal access token`:** the token can't write
here. Classic: the `public_repo` scope is missing. Fine-grained: Repository access was left on the
read-only "Public repositories" default, the repo wasn't selected, or Contents isn't *Read and write*.
Also check you accepted the collaborator invite.

**No token, no problem:** write the patch in the editor, hit **Copy JSON**, then paste the result over
`patches.json` on github.com (pencil icon) and commit there with your normal login.

## Notes formatting

`PatchNotes` supports a markdown subset, rendered identically in the editor preview and in-game
(`UIRRPatchSubsystem::PatchNotesToRichText`): `## ` section header, `- ` bullet, `**bold**`.

## Patch entry format (`patches.json`)

| Key | Meaning |
|---|---|
| `Build` | Version this entry belongs to, e.g. `"#1.0.2.0"`. Must match the game's build format. |
| `Date` | ISO 8601, e.g. `"2026-07-27T12:00:00"`. |
| `Title` | Display title, e.g. `"The Safehouse Update"`. |
| `PatchNotes` | The notes text (markdown subset above). `\n` for line breaks. |
| `SteamAnnouncementURL` | Optional link to the Steam announcement for this patch. |
| `bMajorUpdate` | Marks a major update (highlighted in the history menu). |
| `bDelete*SaveGame` | What this patch wipes: All / GameSettings / Mission / World / Vendor / Stash / Inventory / Safehouse. |

## News entry format (`news.json`)

| Key | Meaning |
|---|---|
| `Id` | **Stable unique id**, e.g. `"2026-07-28-summer-event"`. The game delivers each id once per player, ever — **never change an Id after publishing** (players would receive the entry again as a new message). The editor assigns it automatically (date + title, read-only) and keeps it fixed across edits; only hand-editing the JSON can break this. |
| `Date` | ISO 8601. Used as the inbox message timestamp. |
| `Title` | Message title in the inbox. |
| `Body` | Message text (same markdown subset as patch notes). |
| `LinkURL` | Optional external link (Steam page, Discord, …) shown as a button on the message. |

To retire old news, just delete the entry — players who already received it keep their inbox message;
players who never saw it won't get it anymore.

## Rules

- **Wipe flags are destructive** — a player updating across several versions gets the **union** of all
  missed patches' flags. Double-check before committing; prefer a PR + review for any wipe.
- Entries with a `Build` newer than the installed game never wipe — they only flip the
  "update available" state. So it's safe to publish notes slightly before the build goes live.
- News: keep the list short (newest first); prune outdated events. Editing an entry's text is safe
  (players who already got it keep the old text; it is not re-delivered) — only the `Id` must stay fixed.
