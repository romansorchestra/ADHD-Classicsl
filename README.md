# ADHD Medley Builder

Builds the full "Classical Music for Sufferers of ADHD" draft medley (~12:05)
from a folder of recordings: extracts all 27 excerpts, loudness-matches them
across different recordings (static gain only, so the Haydn surprise and the
1812 cannons keep their dynamics), lays the Boléro snare bed under Pomp &
Circumstance as a true overlay, and chains everything with the designed
transitions — hard cuts, equal-power crossfades, and silences.

**Outputs:** `medley_draft.mp3` (320k) + `medley_draft.wav` + `timeline.txt`
(cumulative timestamps per excerpt — doubles as the orchestrator's map).

## Quick start (Claude Code, zero-effort route)

Open Claude Code in this folder and paste:

> Read README.md. Find a suitable free recording for every file in the
> sources table below (Musopen.org, Archive.org, Wikimedia Commons — prefer
> per-movement files, and one consistent orchestra where possible), save
> them into sources/ with the exact names given, then run
> `python3 build_medley.py --check-only`, fix anything it flags, then
> `python3 build_medley.py --audit`. Give me medley_draft.mp3 and tell me
> which items are marked ESTIMATE in timeline.txt.

Needs `ffmpeg` (`apt-get install -y ffmpeg` if missing) and Python 3. No
Python packages required.

## Sources — exact file names

Any audio format works (.mp3/.wav/.flac/.m4a/.ogg). For a **private**
listening draft, any recordings are fine — recording copyright only matters
if the mock-up is published. "Movement file" = the file should contain just
that movement, starting at its bar 1.

| File name | What it must contain |
|---|---|
| 01_zarathustra | Also sprach Zarathustra (opening at 0:00) |
| 02_beethoven5 | Symphony 5, mvt 1 (movement file) — also used for the final button |
| 03_figaro | Figaro Overture (starts at 0:00) |
| 04_mozart40 | Symphony 40, mvt 1 (movement file) |
| 05_spring | Four Seasons: Spring, mvt 1 (movement file) |
| 06_summer_presto | Four Seasons: Summer, mvt 3 Presto (movement file) |
| 07_cancan | Galop infernal — standalone galop file preferred; if it's the full Orpheus overture, set `start` in cuesheet.json |
| 08_hungarian5 | Hungarian Dance No. 5, orchestral (starts at 0:00) |
| 09_toreador | Carmen Suite 1, "Les Toréadors" (movement file) |
| 10_habanera | Carmen Suite 2, "Habanera" (movement file) |
| 11_danube | Blue Danube waltz, complete |
| 12_morning_mood | Peer Gynt: Morning Mood (movement file) |
| 13_clair_de_lune | Clair de lune (piano is fine for the draft) |
| 14_jupiter | The Planets: Jupiter, complete movement |
| 15_swan_lake | Swan Lake: Scène (swan theme) (movement file) |
| 16_mountain_king | In the Hall of the Mountain King, complete (both ends are used) |
| 17_bald_mountain | Night on Bald Mountain — Rimsky-Korsakov version |
| 18_dies_irae | Verdi Requiem: Dies irae (movement file) |
| 19_valkyries | Ride of the Valkyries, concert version |
| 20_bumblebee | Flight of the Bumblebee, orchestral |
| 21_trepak | Nutcracker: Trepak (movement file) |
| 22_william_tell | William Tell Overture: Finale — standalone finale preferred; if full overture, set `start` (~8:25) |
| 23_haydn_surprise | Symphony 94, mvt 2 (movement file) |
| 24_pomp | Pomp & Circumstance March No. 1, complete |
| 25_bolero_snare | Boléro (only the opening snare bars are used) |
| 26_ode_to_joy | Symphony 9, mvt 4 (movement file) |
| 27_1812 | 1812 Overture, complete (excerpt is taken from the END, so any recording length works) |

## The one human step: verifying 8 timestamps

Most excerpts start at bar 1 of a movement file — those are automatic. Eight
land mid-piece, and different recordings run at different tempos, so their
`start` values in `cuesheet.json` are marked `"verified": false`:

can-can · toreador · danube · jupiter · william tell · haydn · pomp · ode to joy

Workflow (about 5 minutes by ear):

1. `python3 build_medley.py --audit` renders every transition into `joints/`
   as ~10-second clips — listen to just those, not the whole medley.
2. If an entry point is off, nudge that item's `start` in `cuesheet.json`
   (seconds into the source file) and rerun. Rebuilds take under a minute.
3. The Haydn is the timing-critical one: the excerpt must end ON the
   surprise chord, since that chord launches the Boléro snare.

`timeline.txt` flags the same items with `<< ESTIMATE`.

## Tuning the mix

- Per-item level: `target_offset_db` (breather pieces already sit -2 to -3).
- Transitions: each item's `transition_in` — `{"type":"cut"}`,
  `{"type":"crossfade","duration":1.5}`, or `{"type":"silence","gap":0.8}`.
- Boléro bed: the `bed` block on `24_pomp` (`solo` = seconds of snare alone,
  `under` = seconds riding beneath Pomp, `gain_db` = bed level).
- `--stems` exports every processed excerpt to `stems/` if you want to
  audition or rearrange pieces individually.
- Pieces are swapped by editing `cuesheet.json` — no code changes ever needed.
