# TGDetect static publication tracker

This is a complete static site. The browser does the filtering, clock updates, watchlist, calendar export and feed requests. There are no server functions, API keys, database, Python runtime or Node runtime in the deployed website.

## Deploy on Vercel

Use only the `publication-site` folder as the project. It contains the public tracker, venue metadata, CSV, this guide and the parser license. Keep the project repositories, checkpoints, understanding dossier and reference papers outside the public deployment.

1. Put the contents of `publication-site/` in a new Git repository, or use that folder as the Root Directory if it is inside an existing repository.
2. Import that repository in Vercel. Select Framework Preset **Other**.
3. Leave Build Command and Install Command empty. The prepared `vercel.json` disables both and sets Output Directory to `.`. Do not add a server command or environment variables.
4. Deploy and share the resulting Vercel URL. Copy view link includes the current search/domain/type/deadline filters. Each friend keeps their own watchlist in their browser.

The prepared `publication-site.zip` contains the same folder contents for transfer. Unzip it before importing or deploying. You can also open `index.html` directly without a local server; the embedded verified dataset and all local interactions work offline. Online feed checks require internet access.

Vercel official references: [Git deployment](https://vercel.com/docs/git), [project configuration](https://vercel.com/docs/project-configuration), [static-site deployment](https://vercel.com/i/static-site-generator).

## What is live

- Countdowns refresh every 30 seconds from the device clock. Known official times are converted from their timezone; date-only deadlines show calendar days without inventing a cutoff time.
- The next action includes required abstract/registration dates, which can be earlier than the full-paper date.
- Every 15 minutes while visible, the page reloads `tracker-data.json` from the same static host and directly fetches four public Hugging Face AI Deadlines YAML feeds: NeurIPS, ICML, ICLR and AAAI. It also refreshes when returning to the tab or reconnecting.
- Only community deadline types `paper` or `submission` indicate a new-paper opportunity. Reviews, rebuttals, notifications, supplementary files and camera-ready dates are excluded. A future community candidate appears separately from verified dates, with its source and a verification label.
- The maintained community feeds are not official publisher APIs. They may be incomplete, late, mistaken or normalize cutoff times. Their latest check timestamp is shown. A successful fetch does not mean the source recently changed or that the date is officially verified.
- Network or parsing failures retain embedded venue data and previously cached community data. The status reports incomplete coverage. The page flags official research older than 14 days.

The page does **not** guarantee discovering every publisher deadline change. Many publishers do not expose browser-readable machine feeds and their pages cannot reliably be fetched across origins. Official deadlines remain curated until the JSON is updated. Refresh dates checks the available data sources; it does not scrape or reverify every publisher website.

Nothing runs while the page is closed. Export calendar imports dated paper and registration events with a seven-day reminder into a calendar application; calendar imports are snapshots, not subscriptions. Re-export when dates change. Undated/rolling venues do not receive invented calendar events.

## Update verified venue data

Edit `tracker-data.json` in the static project after checking an official CFP/author page, keeping `checked_on` truthful. Each entry has its own source-check date, so updating one source should not mark other sources as newly checked. The page uses those individual dates to display freshness. Keep a maximum of 15 distinct publisher/platform values.

For source-controlled preparation in the local research folder, update `publication-options.json` for the original venues or `ai-venue-research.json` for the five AI additions, then run:

```bash
python3 build-publication-tracker.py
```

This is an optional local preparation step, not a Vercel build requirement. It rebuilds the standalone HTML, static JSON, CSV and `publication-site/` files. Copy the regenerated static files into the deployment repository. Vercel's Git integration deploys subsequent pushes automatically.

Keep `date` null for rolling journals; use an explicit `status: "unannounced"` for a conference without a published deadline. A historical conference date remains closed, with `next_cycle` explaining that the following edition's submission timetable is unannounced. Templates are edition-specific: a 2026 style is not automatically a 2027 style.

## Current coverage

15 publisher/platform websites, 18 venue options: the original IEEE, ACM, Elsevier, Springer Nature, USENIX, Oxford, Taylor & Francis, Wiley, MDPI and arXiv sites, plus NeurIPS, ICML, ICLR, AAAI and JMLR. Multiple venues share IEEE and MDPI, so venue count is higher than website count. Domain tags cover AI, machine learning, data science, cybersecurity and applied AI in security. Fit tags are editorial judgments; check the venue scope before choosing one.

The four checked AI conference main-paper cycles are closed as of 6 October 2026. Their next main-paper deadlines were not located in checked official sources. JMLR is rolling, but requires a general machine-learning contribution rather than solely an application of ML to cybersecurity. arXiv is a moderated preprint platform and provides no peer review.

## Runtime size and third-party assets

The main HTML includes its styling, verified dataset, tracker logic and pinned js-yaml 4.1.1 parser. The parser is vendored rather than loaded from a CDN at runtime. Its MIT license and source hash are in `THIRD-PARTY-NOTICES.txt`. No remote JavaScript executes in this app; only public YAML data is fetched.

Watchlists and cached feed entries use localStorage when available. If browser storage is disabled or full, the current page still works, with persistence unavailable. No personal project files or watchlist contents are sent to the public feeds.
