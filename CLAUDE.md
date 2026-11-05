# CLAUDE.md

This repo is a personal log of tinkering projects (IT, DIY, misc) written in Markdown. There is no code, build or tests.

## Layout

- Categories: `it/{linux,macos,networking,self-hosting}`, `diy/{woodworking,repairs,vehicles}`, `misc/`.
- Each entry is its own folder in kebab-case under a category, e.g. `diy/repairs/dishwasher-pump/`.
- An entry folder holds a `README.md` (the write-up) and an optional `images/` subfolder for photos and screenshots.
- `_template.md` in the root is the template for new entries.
- Empty folders contain a `.gitkeep`. Delete it once the folder has real content.

## Adding an entry

1. Create the entry folder under the matching category. Ask if the category isn't clear, and don't create a new category without asking.
2. Copy `_template.md` into it as `README.md` and fill in the title, date (YYYY-MM-DD) and status (`in progress`, `done` or `abandoned`).
3. Add a link to the **Entries** list in the root `README.md`. If you add a new category, also add it to the **Structure** table.

## Split entries (multiple files)

When an entry gets too long for one file, split it into numbered step files:

- `README.md` is the overview: date, status, tags, Goal, Setup / Materials, a **Steps** table (`#`, linked step title, status), Known issues, Result, Notes / Next time, References.
- Each step is a file named `NN-short-name.md` with a two-digit number (`01-prepare-macos.md`, `02-install-ubuntu.md`), based on `_template-step.md`.
- A step file has: title `# NN · Step title`, a link back to the overview, a one-line goal, numbered sub-steps, a **Check** section, a **Problems & Fixes** table for that step only, and previous/next links at the bottom.
- Problems that affect several steps go into **Known issues** in the overview.
- Image file names start with their step number: `images/02-installer-partitioning.png`.
- When inserting, renaming or removing a step, renumber the files with `git mv` and update the Steps table and every previous/next link.
- Small entries stay a single `README.md`.

## Writing style

- Write it as a practical log for my future self: exact commands, versions, model numbers, part numbers and settings.
- Put commands and config in fenced code blocks with a language tag (`bash`, `yaml`, …).
- Record what failed as well as what worked, under **Problems & Fixes**.
- Link to sources under **References**.
- Use relative links for images: `![description](images/file.jpg)`.
- Use lowercase kebab-case for image file names.

## Don'ts

- Don't invent steps, commands or results I didn't give you. Leave a `TODO:` instead.
- Don't commit secrets: passwords, API keys, Wi-Fi keys, public IPs, serial numbers. Replace them with placeholders like `<WIFI_PASSWORD>`.
- Don't commit unless I ask.
