# FITNESS AI FACTORY (free-first)

A cloud-first YouTube Shorts production starter. It does **not** require your laptop to render videos.
A scheduled GitHub Actions workflow runs on GitHub-hosted Linux runners, fetches three licensed stock shots per Short from Pexels,
creates original English scripts from a curated exercise library, generates a US-English male voice with Piper,
burns timed captions, and exports four vertical MP4 drafts as a downloadable ZIP artifact.

## Important reality check

- This is a **free-first prototype**, not unlimited free AI video generation. It uses real licensed stock footage plus original narration, captions, graphics, and editing—not fully AI-generated human motion.
- GitHub Actions minutes, artifact storage, Pexels API limits, and available runner resources are subject to their current terms and quotas.
- The workflow is configured for **manual approval**: it creates drafts only. It never uploads or publishes to YouTube.
- A stock clip is not automatically a good demonstration of the exact exercise named in a script. Review every draft and replace unsuitable footage before publishing.
- The scripts are educational templates, not individualized medical advice. Use conservative exercise cues and avoid guaranteed weight-loss claims.
- Review Pexels' current license and API terms before commercial use. Keep attribution where required by the asset's license or your own editorial policy.

## What it produces

Each run creates four 9:16 Shorts (default duration follows narration, typically around 25–35 seconds) with three distinct stock shots per video and:
- original US-English script and title
- male US-English Piper narration (`en_US-lessac-medium`)
- burned-in word/phrase captions
- simple branded title/CTA overlays
- background music **omitted by default** to avoid copyright issues
- metadata text file with title, description, hashtags, and source URLs

The result is uploaded as a GitHub Actions artifact named `fitness-shorts-drafts`. Download it from the workflow run page.

## Setup (one time)

1. Create a **public GitHub repository** from this folder (public repositories generally make standard GitHub-hosted Actions usage free; verify current GitHub plan/limits). Do not publish API keys.
2. In the repository, open **Settings → Secrets and variables → Actions → New repository secret**.
3. Add:
   - `PEXELS_API_KEY` = your free Pexels API key from https://www.pexels.com/api/
4. Commit/push the project files to the repository's default branch.
5. Open **Actions** and enable workflows if GitHub asks.
6. Open **Fitness AI Factory – Daily Drafts** → **Run workflow** to test. The scheduled run is set to 14:00 UTC daily; change the cron in `.github/workflows/daily-production.yml` if desired.
7. When the run completes, open it → **Artifacts** → download `fitness-shorts-drafts.zip`.

## Where to get a Pexels API key

Create a Pexels account and request an API key at https://www.pexels.com/api/.
Store the key only as a GitHub Actions secret. Never paste it into source code or commit it.

## Local test (optional)

Python 3.10+ and FFmpeg are required. Install:

```bash
pip install -r requirements.txt
export PEXELS_API_KEY="your-key"
python src/generate_batch.py --output output --count 4
```

On Windows PowerShell:

```powershell
$env:PEXELS_API_KEY="your-key"
python src/generate_batch.py --output output --count 4
```

Your laptop is only needed for optional local testing or downloading/reviewing artifacts. The cloud workflow performs rendering.

## Change the channel brand

Edit `src/config.py`:
- `CHANNEL_NAME`
- `BRAND_TAGLINE`
- `ACCENT_COLOR`
- `VOICE_MODEL`

## Change the content

Edit `content/exercises.json`. Each topic has:
- `category`, `hook`, `title`, `steps`, `safety`, `search_terms`, `cta`

Keep exercise instructions accurate. Add only movements you can confidently demonstrate and explain.

## Free tier and reliability notes

GitHub Actions is an automation runner, not a permanent server. Scheduled runs can be delayed, and public repo schedules may be disabled after long inactivity. Runs may fail if a third-party API is unavailable or the stock API has no suitable result. This project exits clearly rather than silently generating a misleading exercise video.

The Pexels API is a dependency. If you do not add a key, the workflow stops with a clear error. Do not scrape Pexels or bypass API controls.

## Quality-control checklist (before publishing)

- Does the footage show the exact movement described?
- Are joint positions and range of motion safe and understandable?
- Does the narration match the footage?
- Are captions readable and correctly spelled?
- Is the video genuinely useful/original rather than a repetitive stock montage?
- Are there no unlicensed logos, music, or misleading before/after claims?
- Does the description accurately disclose synthetic narration/AI assistance where appropriate?

## Troubleshooting

- `PEXELS_API_KEY is missing`: add the repository secret and rerun.
- No footage returned: try changing `search_terms` in `content/exercises.json`.
- Piper model download issue: rerun; GitHub runner is ephemeral and downloads the model each run.
- FFmpeg error: inspect the action log; the workflow installs FFmpeg from Ubuntu packages.
- Artifact too large: reduce `CLIP_SECONDS` or `COUNT` in `src/config.py`.

## Project layout

```text
.github/workflows/daily-production.yml  scheduled cloud job
content/exercises.json                  editable topic library
src/config.py                           channel/render settings
src/generate_batch.py                   pipeline entry point
src/pexels.py                           licensed stock search/download
src/voice.py                            Piper TTS
src/render.py                           FFmpeg vertical composition
src/captions.py                         SRT generation
requirements.txt                        Python dependencies
```
