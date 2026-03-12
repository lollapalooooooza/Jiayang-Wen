---
name: weekly-vibe-updater
description: Update a Weekly Vibe Coding card in the Jiayang Wen portfolio when the user wants to change its cover image, semi-transparent image treatment, emoji, title, description, try-out link, or GitHub README link from either a supplied spec file or a GitHub repo URL whose README should be converted into a draft spec.
---

# Weekly Vibe Updater

Use this skill when working in the `jiayangwen` portfolio repo and the user wants to update one card in the `#vibecoding` section of [`index.html`](/Users/bryanwen/Documents/New%20project/jiayangwen/index.html).

## What this skill changes

For one target vibe card, update:

- the cover into a semi-transparent image background
- the emoji
- the title
- the description
- the primary link from `Download` to `Try it out`
- the secondary link from generic GitHub to the specific README URL

## Input modes

This skill supports two entry modes.

### Mode A: Filled template file

Ask the user for a filled template file if they did not already upload one.

Use the template at [`references/vibe-update-template.yaml`](/Users/bryanwen/Documents/New%20project/jiayangwen/.codex/skills/weekly-vibe-updater/references/vibe-update-template.yaml).

The update file should provide:

- `target_week`
- `cover_image`
- `cover_alt`
- `emoji`
- `title`
- `description`
- `try_out_url`
- `readme_url`

### Mode B: GitHub repo URL

If the user provides a public GitHub repo link instead of a filled template:

1. Read the README from that repo.
2. Draft the same YAML fields that the template requires.
3. Infer values conservatively:
   - `title`: project name or README title
   - `description`: one concise sentence based on the README
   - `readme_url`: canonical GitHub README URL
   - `try_out_url`: GitHub Pages URL only if it is explicit in the README or can be directly derived from a standard Pages deployment already linked by the repo or user
   - `emoji`: choose one emoji that matches the project's main mood or interaction style
   - `cover_alt`: short plain description of the intended cover image
4. Do not invent a cover image path. If the user has not provided one, leave `cover_image` as `TBD` and ask for the asset.
5. Return the drafted YAML for review before editing the site.

## Workflow

### Draft mode

Use this flow when starting from a GitHub repo link.

1. Read the repo README.
2. Draft the YAML using the template fields.
3. Clearly label anything inferred from the README.
4. Ask the user to confirm or adjust the draft.
5. Do not edit the site until the user approves the draft.

### Apply mode

Use this flow after the user approves a draft or uploads a completed template.

1. Read the approved spec file or approved draft first.
2. Open [`index.html`](/Users/bryanwen/Documents/New%20project/jiayangwen/index.html) and locate the `#vibecoding` section.
3. Find the `.vibe-card` whose `.vibe-week` matches `target_week`.
4. If the card does not already support an image cover:
   - add a reusable cover structure inside the card
   - add or update reusable CSS once near the existing `.vibe-card` rules
   - keep the rest of the section visually consistent with the site
5. Set the card to use the requested cover image with a semi-transparent overlay so text remains readable.
6. Replace the emoji, title, description, and both links.
7. The primary button label must be `Try it out`.
8. The secondary button label should clearly indicate README access, for example `Readme`.
9. Keep paths relative to the repo when the uploaded file references a local asset such as `images/foo.png`.
10. If the requested image file is missing, stop and tell the user exactly which path is missing.
11. After editing, if the user explicitly asked for a push, check git status, commit the change, and push the current branch.

## Editing rules

- Edit only the CSS and the single target card unless another shared refactor is required for the cover treatment.
- Do not change unrelated sections.
- Preserve existing typography and spacing unless a small adjustment is required for readability over the image.
- Keep links opening in a new tab.
- For README-derived drafts, prefer exact facts from the repo over marketing language.
- If a try-out URL is not verified, mark it as `TBD` instead of guessing.

## Expected user prompt

Typical prompt:

`Use $weekly-vibe-updater with the uploaded spec file to update the weekly vibe block in jiayangwen.`

Repo-to-draft prompt:

`Use $weekly-vibe-updater on this GitHub repo link, draft the weekly vibe YAML for review, and wait for approval before changing the site.`

## Output

In draft mode:

- show the drafted YAML in a fenced code block
- note which fields were inferred
- call out any missing `cover_image` or `try_out_url`

After making the change:

- summarize which week was updated
- name the cover image path used
- list the final try-out URL and README URL
- mention any missing asset or validation issue if applicable
- if a push was requested, report whether commit and push succeeded
