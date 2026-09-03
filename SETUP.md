# Setup

## 1. Create your profile repo
GitHub renders `README.md` from a repo whose name matches your username, at
the top of your profile page.

```bash
gh repo create YOUR_USERNAME --public --clone
cd YOUR_USERNAME
# copy every file from this project into the new repo (README.md, scripts/, .github/)
```

## 2. Edit scripts/config.py
Set:
- `GITHUB_USERNAME` — your actual GitHub username
- `CARD_TITLE` and `CARD_ROWS` — whatever you want on the neofetch card
  (Now / Prev / Stack / Highlights, or add/remove rows)

## 3. Install dependencies
```bash
python -m venv .venv && source .venv/bin/activate
pip install -r scripts/requirements.txt
```

## 4. Generate everything once, locally
```bash
python scripts/prep_photo.py path/to/your-photo.jpg   # writes scripts/source-prepped.png
python scripts/make_ascii_svg.py                      # writes avi-ascii.svg
python scripts/make_info_card.py                      # writes info-card.svg
python scripts/fetch_contributions.py                 # writes data/contributions.json
python scripts/render_heatmap_svg.py                  # writes contrib-heatmap.svg
```

Open the .svg files directly in a browser to preview the animations before
committing (double-click, or `open avi-ascii.svg` on macOS).

## 5. Commit and push
```bash
git add .
git commit -m "Add animated profile README"
git push
```

Visit `github.com/YOUR_USERNAME` to see it live.

## 6. Turn on the daily auto-refresh
The workflow in `.github/workflows/update-profile-art.yml` re-scrapes your
contributions and re-renders the heatmap every day at ~06:17 UTC, then
commits the result back automatically. It only needs `requests` and
`beautifulsoup4` — not the heavier portrait libraries.

Trigger it once by hand to confirm it works:
GitHub repo → **Actions** tab → **Update profile art** → **Run workflow**.

## Notes
- The ASCII portrait and info card are static — regenerate them locally
  only when you change your photo or your card content, then commit.
- If GitHub changes its contribution-calendar HTML markup in the future,
  `fetch_contributions.py`'s `parse_days()` is the one function to update —
  it currently reads `<td class="ContributionCalendar-day">` cells and
  their paired `<tool-tip for="...">` elements for the count text.
- Inline `style=` attributes are stripped by GitHub's markdown sanitizer —
  only `<br>` gives you vertical spacing in the README itself. That
  restriction doesn't apply inside the SVG files, which is why all the
  actual styling/animation lives there instead of in README.md.
