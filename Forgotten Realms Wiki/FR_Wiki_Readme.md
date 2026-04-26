# Forgotten Realms Wiki → Obsidian Vault Converter

Convert the Forgotten Realms Wiki into a beautifully organised [Obsidian](https://obsidian.md) vault — complete with working `[[wikilinks]]`, ITS Theme infoboxes, redirect resolution, broken link cleanup, and notes sorted into folders by category.

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Content License: CC BY-SA 3.0](https://img.shields.io/badge/Content-CC--BY--SA--3.0-blue.svg)
![Python: 3.8+](https://img.shields.io/badge/Python-3.8%2B-green.svg)

---

## What Does This Script Do?

This script takes the Forgotten Realms Wiki XML data dump and converts it into individual Markdown notes, one per wiki article, ready to open in Obsidian. Each note includes:

- **Rich article content** — all the lore text, fully converted to Markdown
- **Working `[[wikilinks]]`** — links between notes that Obsidian can follow and graph
- **Redirect resolution** — links like `[[Bloodhand Hold]]` automatically point to the correct note (`[[Waterdeep|Bloodhand Hold]]`), including full redirect chain resolution and URL-decoded links
- **Slash link sanitization** — links like `[[Waterdeep/Dock Ward]]` are rewritten to `[[Waterdeep-Dock Ward]]` to match actual filenames on disk
- **Broken link cleanup** — any remaining links that point to notes that don't exist are automatically converted to plain text
- **ITS Theme infoboxes** — place articles display a Wikipedia-style infobox showing type, region, population, government, races, religion, and more
- **YAML frontmatter** — each note has structured metadata Obsidian can search and filter
- **Organised subfolders** — notes are automatically sorted using infobox type detection and keyword matching into folders like `Places/Settlements`, `Characters/Wizards & Mages`, `Creatures/Dragons`, `Deities & Religion`, and more

### Two Modes

**Full mode** — converts the entire wiki (~68,000 notes):
```
python fr_to_obsidian.py --input dump.xml --output FR-Vault
```

**Map mode** — converts only articles linked from an Obsidian map plugin JSON file, plus all places those articles link to (~25,000 notes for the Sword Coast map):
```
python fr_to_obsidian.py --input dump.xml --output FR-Vault --json map_markers.json
```

Map mode is ideal if you're running a campaign in a specific region and want a focused, fast vault rather than the full 68,000 article set.

---

## Before You Start — What You'll Need

### 1. The FR Wiki XML Dump

You need to download the wiki data file first. This is a single large XML file (~390 MB) containing all the wiki's text content.

1. Go to: **https://forgottenrealms.fandom.com/wiki/Special:Statistics**
2. Scroll down to the **"Database dumps"** section
3. Click the date link next to **"Current pages"** to download
4. The file will be named something like `forgottenrealms_pages_current.xml.7z`
5. You'll need to **extract** the `.7z` file to get the `.xml` inside it:
   - **Windows**: Download [7-Zip](https://www.7-zip.org/) (free), right-click the file → "7-Zip" → "Extract Here"
   - **Mac**: Download [The Unarchiver](https://theunarchiver.com/) (free) from the App Store, then double-click the `.7z` file
   - **Linux**: Run `7z x forgottenrealms_pages_current.xml.7z` in terminal

> **Note on content freshness:** The XML dump reflects each article as of its last edit date. Some articles may have been updated on the live wiki after the dump was generated. The dump typically covers edits up to a few months before the download date. There is no workaround for this — it is a limitation of the dump-based approach.

### 2. Python 3.8 or Later

Python is the programming language this script runs on. Check if you already have it:

- Open **Terminal** (Mac/Linux) or **Command Prompt** (Windows) and type:
  ```
  python --version
  ```
  or
  ```
  python3 --version
  ```
- If you see `Python 3.8` or higher, you're good. Skip to Step 3.
- If you get an error or see Python 2.x, you need to install Python.

**Installing Python:**

| System | Instructions |
|--------|-------------|
| **Windows** | Go to [python.org/downloads](https://www.python.org/downloads/), download the latest installer. **Important:** on the first screen of the installer, tick the box that says **"Add Python to PATH"** before clicking Install. |
| **Mac** | Go to [python.org/downloads](https://www.python.org/downloads/) and download the macOS installer. Alternatively, if you have [Homebrew](https://brew.sh/), run `brew install python3` |
| **Linux** | Run `sudo apt install python3` (Ubuntu/Debian) or `sudo dnf install python3` (Fedora) |

After installing, **close and reopen** your terminal/command prompt window.

### 3. Obsidian (with ITS Theme — optional but recommended)

- Download Obsidian free from [obsidian.md](https://obsidian.md)
- The script generates ITS Theme infoboxes for place articles. To see these render correctly, install the **ITS Theme**:
  - In Obsidian: Settings → Appearance → Themes → Browse → search "ITS" → Install
- If you don't use ITS Theme the notes still work perfectly — the infobox just appears as a standard callout block

---

## Installation

### Step 1 — Download the Script

Click the green **"Code"** button at the top of this GitHub page, then click **"Download ZIP"**. Extract the ZIP file somewhere on your computer — for example:

- **Windows**: `C:\FR-Wiki-Tool\`
- **Mac/Linux**: `~/Documents/FR-Wiki-Tool/`

You only need the file `fr_to_obsidian.py`.

### Step 2 — Install the Optional Dependency

The script works without this, but installing `tqdm` adds a progress bar so you can see how far along the conversion is.

Open Terminal (Mac/Linux) or Command Prompt (Windows) and run:

```
python -m pip install tqdm
```

or on Mac/Linux if that doesn't work:

```
python3 -m pip install tqdm
```

---

## Running the Script

Open **Terminal** (Mac/Linux) or **Command Prompt** (Windows).

### Full Mode — All 68,000 Notes

**Windows:**
```
python fr_to_obsidian.py --input "D:\Downloads\forgottenrealms_pages_current.xml" --output "D:\Obsidian\FR-Vault"
```

**Mac:**
```
python3 fr_to_obsidian.py --input ~/Downloads/forgottenrealms_pages_current.xml --output ~/Documents/FR-Vault
```

**Linux:**
```
python3 fr_to_obsidian.py --input /path/to/forgottenrealms_pages_current.xml --output /path/to/FR-Vault
```

---

### With Images — Linked from Fandom CDN

Adding `--images` embeds article and infobox images directly from the Fandom CDN. No files are downloaded — images load from the web when you view a note. Use `--image-width` to cap the display width of body images (the infobox image is always uncapped — ITS Theme handles its sizing automatically).

**Windows:**
```
python fr_to_obsidian.py --input "D:\Downloads\forgottenrealms_pages_current.xml" --output "D:\Obsidian\FR-Vault" --images --image-width 400
```

**Mac/Linux:**
```
python3 fr_to_obsidian.py --input dump.xml --output FR-Vault --images --image-width 400
```

Both flags work with map mode too:
```
python3 fr_to_obsidian.py --input dump.xml --output FR-Vault --json map.json --images --image-width 400
```

`--images` is **off by default** — omitting it produces the same output as before.

---

### Map Mode — Focused on Your Campaign Region

If you use the [Obsidian Leaflet](https://github.com/javalent/obsidian-leaflet) or similar map plugin and have a JSON file with map pins, you can run in map mode. The script will extract only the articles linked from your map pins, then follow all links from those place articles outward — pulling in everything geographically connected to your map while skipping unrelated content.

**Windows:**
```
python fr_to_obsidian.py --input "D:\Downloads\forgottenrealms_pages_current.xml" --output "D:\Obsidian\FR-Vault" --json "D:\Obsidian\Maps\my_map_markers.json"
```

**Mac/Linux:**
```
python3 fr_to_obsidian.py --input dump.xml --output FR-Vault --json my_map_markers.json
```

The JSON file must be in Obsidian Leaflet marker format, where each marker has a `"link"` field containing the article name:
```json
{
  "markers": [
    { "type": "pin", "link": "Waterdeep", ... },
    { "type": "pin", "link": "Daggerford", ... }
  ]
}
```

**How map mode traversal works:**
1. Starts with your map pin names as seeds
2. Follows all `[[wikilinks]]` from **place articles** (towns, regions, buildings, roads etc.)
3. Non-place articles (characters, creatures, spells, items) are included if linked from a place, but links *from* them are not followed further — keeping the vault geographically anchored
4. Result: a focused vault of ~25,000 notes for a region like the Sword Coast

---

## How Long Does It Take?

| Mode | Passes | Time (approx) |
|------|--------|---------------|
| **Full mode** | Pass 1: redirect map (~1 min) → Pass 2: count articles (~1 min) → Pass 3: convert & write (~5–8 min) → Post: fix broken links (~2 min) | **~10–12 min** |
| **Map mode** | Pass 1: redirect map (~1 min) → Pass 2: build allowlist (~2 min) → Pass 3: convert & write (~3–4 min) → Post: fix broken links (~1 min) | **~7–8 min** |

---

## What the Script Prints

```
Building redirect map (pass 1)...
  Found 14,538 redirects. Resolving chains...
  Redirect map ready (14,304 entries).

  Loaded 254 map pins from my_map_markers.json    ← map mode only
Building article allowlist from map pins...        ← map mode only
  Allowlist: 28,968 articles to extract.

Processing 28,968 articles from allowlist.

Fixing broken links...
  Scanning 25,960 notes...
  Fixed 82,608 broken links across 14,070 notes

====================================================
  Notes written  : 25,960
  Links resolved : 17,007
  Skipped        : 3,008
====================================================
```

---

## Opening the Vault in Obsidian

1. Open Obsidian
2. Click **"Open folder as vault"**
3. Navigate to and select the output folder you specified (e.g. `FR-Vault`)
4. Obsidian will index all the notes — this takes a minute or two the first time
5. Press `Ctrl+G` (Windows/Linux) or `Cmd+G` (Mac) to open the **Graph View**

---

## Output Structure

The vault is organised into subfolders automatically based on the article's infobox type and content:

```
FR-Vault/
├── Places/
│   ├── Settlements/        (cities, towns, villages — detected from Location infobox type)
│   ├── Regions & Nations/  (kingdoms, empires, duchies)
│   ├── Geography/          (mountains, forests, rivers, seas)
│   ├── Fortresses & Ruins/ (castles, dungeons, keeps)
│   ├── Buildings/          (temples, inns, shops — Building infobox)
│   ├── Planes/             (Abyss, Astral Plane, etc.)
│   └── Roads & Paths/      (trade routes, trails — Road infobox)
├── Characters/
│   ├── Wizards & Mages/    (detected from Person infobox + content keywords)
│   ├── Warriors/
│   ├── Priests & Paladins/
│   ├── Rogues/
│   ├── Rulers & Nobles/
│   ├── Rangers & Druids/
│   ├── Bards/
│   └── Monks/
├── Creatures/
│   ├── Dragons/
│   ├── Undead/
│   ├── Elves/
│   ├── Dwarves/
│   ├── Fiends/
│   └── ... (more)
├── Deities & Religion/     (Deity infobox)
├── Organizations/          (Organization infobox)
├── Magic/
│   ├── Spells/             (Spell infobox)
│   ├── Items & Artifacts/  (Item infobox)
│   └── Lore/
├── History/
│   ├── Battles & Wars/
│   └── Events/
├── Language & Culture/
└── Miscellaneous/
```

---

## Example Note — Daggerford

Here's what a generated place note looks like in Obsidian with the ITS Theme:

```markdown
---
title: "Daggerford"
category: Settlements
source_url: "https://forgottenrealms.fandom.com/wiki/Daggerford"
attribution: "Forgotten Realms Wiki (forgottenrealms.fandom.com), CC BY-SA 3.0"
location_type: "Settlement"
region: "Delimbiyr Vale, Sword Coast"
size: "Small town"
races: "Dwarves, halflings, humans"
religion: "Amaunator, Chauntea, Lathander, Tempus, Tymora"
government: "Oligarchy"
ruler: "Council of Guilds"
imports: "Green wood"
exports: "Furs, garments"
population: "300 (1000 overall)"
tags:
  - forgotten-realms
  - settlements
---
# Daggerford

> [!infobox|right]+
> # Daggerford
> ![Daggerford](https://static.wikia.nocookie.net/forgottenrealms/images/0/03/NWN.DoD_cinematic_Daggerford.png)
>
> ###### Details
> | | |
> |---|---|
> | **Type** | Settlement |
> | **Region** | [[Delimbiyr Vale]], [[Sword Coast]] |
> | **Size** | Small town |
> | **Government** | Oligarchy |
> | **Ruler** | [[Council of Guilds]] |
> | **Population** | 300 (1000 overall) |
> | **Races** | [[Dwarves]], [[halfling]]s, [[human]]s |
> | **Religion** | [[Amaunator]], [[Chauntea]], [[Lathander]]... |

**Daggerford** was a small but consequential town located in the 
[[Delimbiyr Vale]] within the greater [[Sword Coast]]...
```

Note that:
- The infobox **floats right** with article text wrapping around it (ITS Theme required)
- When `--images` is used, the infobox image appears at the top of the callout (as shown above)
- Wikilinks **inside** the infobox rows are preserved and clickable
- Links like `[[Waterdeep-Dock Ward|Dock Ward]]` correctly resolve to the note on disk
- The YAML frontmatter fields are searchable in Obsidian's search and Dataview plugin

---

## Known Limitations

**Content freshness** — The XML dump reflects each article as of its last edit. Articles updated on the live Fandom wiki after the dump date will show older content. This is a limitation of the dump-based approach and cannot be worked around in the script.

**Images** — Images are not included by default. Use `--images` to embed them from the Fandom CDN (requires an internet connection when viewing notes). Use `--image-width N` to cap the display width of body images in pixels — the infobox image is always uncapped. Images load from the web on demand; nothing is downloaded to disk.

**Very short articles** — Stub articles with almost no content after template removal are skipped to avoid creating empty notes. These represent a small fraction of the wiki.

**Complex tables** — Some wikitext tables with unusual formatting may not convert cleanly to Markdown. The content is preserved as best as possible.

---

## Troubleshooting

**"python is not recognized" (Windows)**
→ Python wasn't added to PATH during installation. Reinstall Python and make sure to tick **"Add Python to PATH"** on the first screen.

**"No such file or directory" error**
→ Check your file paths. On Windows, make sure you're using quote marks around paths that contain spaces: `"D:\My Files\example.xml"`

**The script seems frozen / no output**
→ It's working — the first passes have no progress bar. Wait a few minutes. If you installed `tqdm`, you'll see a progress bar during the conversion pass.

**"ModuleNotFoundError: No module named 'tqdm'"**
→ Run `python -m pip install tqdm` then try again. The script also works without it — just no progress bar.

**Notes are missing for some places**
→ Some wiki pages have very little content (stubs). These are skipped. Also check whether the article exists in the wiki at all — some red links on Fandom point to pages that were never created.

**The infobox isn't rendering correctly**
→ Make sure you have the ITS Theme installed in Obsidian (Settings → Appearance → Themes → search "ITS").

**A note has a broken link even after running**
→ The link likely points to an article that exists on Fandom but was not included in the vault (e.g. it's outside the map region in map mode, or was a stub that got skipped). The broken link fixer converts these to plain text automatically — if you still see one, try re-running the script with a fresh output folder.

**Running the script again creates duplicate notes**
→ Delete the output folder before re-running to get a clean result. The script adds a number suffix (`Waterdeep_1.md`) if a file already exists rather than overwriting.

**"PermissionError: [Errno 13] Permission denied" (Linux / Mac)**
→ The XML file extracted from the `.7z` archive may have no read permissions. Fix it by running this command in the same folder as the XML file before running the script:
```
chmod 644 forgottenrealms_pages_current.xml
```

---

## Frequently Asked Questions

**Can I use a different map JSON file?**
Yes — any JSON file in Obsidian Leaflet marker format with `"link"` fields containing article names will work. The script reads the `link` field from every marker with `"type": "pin"`.

**Will this work with future XML dumps?**
Yes. The FR Wiki releases updated XML dumps periodically. You can re-run the script against a newer dump to get updated notes. Delete the old output folder first.

**Does it embed images?**
Not by default. Use `--images` to link images from the Fandom CDN. They load from the web when you view a note — nothing is downloaded to disk. Use `--image-width 400` (or any pixel value) to cap the display width of body images.

**Is this legal?**
The FR Wiki text is licensed under CC BY-SA 3.0 which permits this use provided attribution is given — the script adds attribution automatically to every note's frontmatter. See the Licensing section below.

**Can I share the generated vault?**
Yes, provided you share it under CC BY-SA 3.0 and include attribution to the Forgotten Realms Wiki. The attribution is already in every note.

**Can I use this with other wikis?**
The script is tuned specifically for the Forgotten Realms Wiki's templates and infobox structure. It may work partially with other Fandom wikis but results will vary.

---

## Licensing

This project has two separate licences covering two different things.

### The Script Code

`fr_to_obsidian.py` is released under the **MIT License**. You are free to use, modify, and distribute it for any purpose. See `LICENSE-CODE` for the full terms.

### The Generated Vault Content

The notes produced by this script are derivative works of the **Forgotten Realms Wiki** (forgottenrealms.fandom.com), which is licensed under the **Creative Commons Attribution-Share Alike 3.0 (CC BY-SA 3.0)** license.

This means:
- You may use and share the generated vault freely
- You must give credit to the Forgotten Realms Wiki (the script does this automatically in each note's frontmatter)
- If you share the vault publicly, you must do so under the same CC BY-SA 3.0 license

### Wizards of the Coast Fan Content Policy

The Forgotten Realms setting and all associated lore is the intellectual property of Wizards of the Coast LLC. This tool and any content it generates falls under the [Wizards of the Coast Fan Content Policy](https://company.wizards.com/en/legal/fancontentpolicy).

> *This tool uses trademarks and/or copyrights owned by Wizards of the Coast LLC, used under the Wizards of the Coast Fan Content Policy. We are expressly prohibited from charging you to use or access this content. This tool is not published, endorsed, or specifically approved by Wizards of the Coast.*

**Generated vaults are for personal, non-commercial use only.**

---

## Contributing

Found a bug? Have an improvement? Pull requests are welcome. Some areas that could use work:

- Better category detection for edge cases
- Support for additional infobox types
- Support for other Fandom/MediaWiki wikis
- GUI wrapper for non-technical users

Please open an issue first to discuss any significant changes.

---

## Acknowledgements

- **The Forgotten Realms Wiki community** — for their incredible years of work documenting the Realms
- **Wizards of the Coast** — for the Forgotten Realms setting
- **SlRvb** — for the [ITS Theme](https://github.com/SlRvb/Obsidian--ITS-Theme) and infobox callout design
- **WikiTeam** — for their work on wiki preservation tools
