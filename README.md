<div align="center">

# 🔗 Link to Metrics

### _Paste a social media link. Get every metric that matters._

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Pandas](https://img.shields.io/badge/Pandas-Data_Engine-150458?style=for-the-badge&logo=pandas&logoColor=white)](https://pandas.pydata.org)
[![Google Sheets](https://img.shields.io/badge/Google_Sheets-I/O-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)](https://developers.google.com/sheets/api)
[![License](https://img.shields.io/badge/License-Proprietary-red?style=for-the-badge)](https://varaheanalytics.com)

<br/>

<img src="https://img.shields.io/badge/Instagram-E4405F?style=flat-square&logo=instagram&logoColor=white" />
<img src="https://img.shields.io/badge/Facebook-1877F2?style=flat-square&logo=facebook&logoColor=white" />
<img src="https://img.shields.io/badge/YouTube-FF0000?style=flat-square&logo=youtube&logoColor=white" />
<img src="https://img.shields.io/badge/X_(Twitter)-000000?style=flat-square&logo=x&logoColor=white" />

<br/><br/>

> **An enterprise-grade, multi-platform social media metrics extraction engine**  
> Built at [Varahe Analytics Pvt. Ltd.](https://varaheanalytics.com) to power real-time campaign performance dashboards, influencer audits, and content analytics — at scale.

<br/>

| Input | Output |
|:---:|:---:|
| 📋 Google Sheet with post URLs | 📊 Fully enriched Google Sheet with metrics |
| (Any mix of IG, FB, YT, X links) | (Likes, Comments, Shares, Views, Followers, Thumbnails & more) |

</div>

---

## 🎯 What This Does

**Link to Metrics** is an automated pipeline that takes raw social media post URLs and returns structured engagement data — no manual copying, no platform-hopping, no wasted hours.

You drop links into a Google Sheet. The system:

1. **Detects** which platform each link belongs to (Instagram, Facebook, YouTube, or X)
2. **Routes** each link to the appropriate scraping/API engine
3. **Extracts** post-level metrics in parallel across all four platforms simultaneously
4. **Enriches** data with thumbnails (compressed & hosted on GCS)
5. **Writes** a beautifully formatted output back to Google Sheets — orange headers, EB Garamond font, centered alignment, black borders

All of this happens in **one single run**. Handles hundreds of links per execution.

---

## 📊 Metrics Extracted Per Post

| Metric | IG | FB | YT | X |
|:---|:---:|:---:|:---:|:---:|
| **Likes / Reactions** | ✅ | ✅ | ✅ | ✅ |
| **Comments** | ✅ | ✅ | ✅ | ✅ |
| **Shares / Retweets** | ✅ | ✅ | — | ✅ |
| **Views / Reach** | ✅ | ✅ | ✅ | ✅ |
| **Engagement Score** | ✅ | ✅ | ✅ | ✅ |
| **Post Caption** | ✅ | ✅ | ✅ | ✅ |
| **Post Date** | ✅ | ✅ | ✅ | ✅ |
| **Page / Channel Name** | ✅ | ✅ | ✅ | ✅ |
| **Follower Count** | ✅ | ✅ | ✅ | ✅ |
| **Thumbnail** | ✅ | ✅ | ✅ | ✅ |

> **Engagement** = Likes + Comments + Shares (platform-specific formula)

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        📋 Google Sheet (Input)                      │
│                    Post Links  |  State  |  Page Name               │
└─────────────────────────┬───────────────────────────────────────────┘
                          │
                          ▼
                 ┌────────────────┐
                 │    main.py     │
                 │  (Orchestrator)│
                 └───────┬────────┘
                         │
              ┌──────────┼──────────────────────────┐
              │          │                           │
              ▼          ▼                           ▼
     ┌──────────┐  ┌──────────────┐         ┌──────────────┐
     │ read_sheet│  │  params.yaml │         │  write_sheet  │
     │ (gspread) │  │  (config)    │         │  (formatted)  │
     └─────┬────┘  └──────────────┘         └──────▲───────┘
           │                                       │
           ▼                                       │
  ┌────────────────────────────────────────────────┤
  │          function_influencer.py                 │
  │        ┌─── URL Platform Router ───┐           │
  │        │                           │           │
  │   ┌────┴────┐ ┌────────┐ ┌───────┐│┌────────┐ │
  │   │ is_fb() │ │ is_ig()│ │is_yt()│││ is_x() │ │
  │   └────┬────┘ └───┬────┘ └───┬───┘│└───┬────┘ │
  │        │          │          │     │    │      │
  │   ═════╪══════════╪══════════╪═════╪════╪══════╪══════
  │   ║    ▼          ▼          ▼     ║    ▼      ║
  │   ║ ┌──────┐  ┌──────┐  ┌──────┐  ║ ┌──────┐  ║
  │   ║ │  FB  │  │  IG  │  │  YT  │  ║ │  X   │  ║
  │   ║ │Engine│  │Engine│  │Engine│  ║ │Engine│  ║
  │   ║ └──┬───┘  └──┬───┘  └──┬───┘  ║ └──┬───┘  ║
  │   ║    │         │         │       ║    │      ║
  │   ║  ThreadPoolExecutor (Parallel) ║           ║
  │   ═════╪══════════╪══════════╪═════╪════╪══════╪══════
  │        │          │          │          │      │
  │        └──────────┴──────┬───┴──────────┘      │
  │                          │                     │
  │                          ▼                     │
  │                ┌──────────────────┐            │
  │                │  Thumbnail Engine │            │
  │                │  (Playwright/GCS) │            │
  │                └────────┬─────────┘            │
  │                         │                      │
  │                         ▼                      │
  │                  ┌─────────────┐               │
  │                  │  pd.concat  │───────────────┘
  │                  │  (merge all)│
  │                  └─────────────┘
  └────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       📊 Google Sheet (Output)                      │
│   Post Links | Likes | Comments | Shares | Engagement | Views ...   │
│            ✨ Formatted: Orange headers, EB Garamond, Borders       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Platform Engines — Deep Dive

<details>
<summary><b>📸 Instagram Engine</b> — <code>function_ig.py</code></summary>

<br/>

**Dual-source extraction** for maximum reliability:

| Source | What It Fetches | Fallback |
|---|---|---|
| **Apify** (Primary) | Likes, Comments, Shares, Views, Caption, Post Date, Owner | Auto-retry on failed URLs |
| **BrightData** (Secondary) | Same metrics via different pipeline | Activates when Apify fails |

**Key Features:**
- 🔄 **Smart retry logic** — failed URLs are automatically re-queued for a second pass
- 👤 **Follower enrichment** — after scraping posts, a separate Apify actor fetches follower counts for each unique page
- 📊 **Engagement formula:** `Likes + Comments + Shares`
- Handles both `/p/` (photo) and `/reel/` URL formats

</details>

<details>
<summary><b>📘 Facebook Engine</b> — <code>functions_fb.py</code></summary>

<br/>

**Multi-step pipeline** to handle Facebook's notoriously complex URL structures:

1. **URL Resolution** — follows redirects to resolve `fb.watch`, `m.facebook.com`, and short links
2. **Video URL normalization** — converts `/videos/` and `/reel/` paths to `/watch/?v=` format
3. **BrightData scraping** — triggers dataset collection with CSV polling
4. **Reaction aggregation** — sums all reaction types (Like, Love, Wow, Haha, Sad, Angry)
5. **Supermetrics fallback** — fetches share counts via Supermetrics API when BrightData doesn't provide them

**Handles:**
- `facebook.com/reel/`, `facebook.com/share/p/`, `fb.watch/`, `posts/pfbid...`, `watch?v=`
- Automatic `crawl_failed` retry mechanism

</details>

<details>
<summary><b>🎬 YouTube Engine</b> — <code>function_yt.py</code></summary>

<br/>

**Direct API integration** with YouTube Data API v3:

- Supports **all URL formats**: `youtu.be/`, `youtube.com/watch?v=`, `youtube.com/shorts/`, community `post/`
- Fetches **video statistics** + **snippet** (title, description, publish date)
- Separately queries **channel subscriber count** per video
- Picks the **best available thumbnail** (maxres → standard → high → medium → default)
- Progress bar with `tqdm` for large batches

</details>

<details>
<summary><b>🐦 X (Twitter) Engine</b> — <code>function_x.py</code></summary>

<br/>

**Apify-powered tweet extraction:**

- Extracts tweet IDs from URLs using regex (`/status/{id}`)
- Fetches via Apify's Twitter scraper actor with smart filtering
- Extracts **media thumbnails** from `extendedEntities`
- Pulls **author follower count** and **display name** from nested author object
- **Engagement formula:** `Likes + Retweets + Replies`
- Filters out mock/empty tweets automatically

</details>

<details>
<summary><b>🖼️ Thumbnail Engine</b> — <code>fb_ig_thumbnail.py</code></summary>

<br/>

**Intelligent thumbnail extraction & hosting pipeline:**

```
Post URL → Extract og:image → Download → Compress → Upload to GCS → Public URL
```

| Stage | Technology | Details |
|---|---|---|
| **Extraction** | BeautifulSoup / Playwright | Primary: `og:image` meta tag. Fallback: Playwright headless browser |
| **Compression** | Pillow (PIL) | JPEG compression at configurable quality (default: 30) |
| **Hosting** | Google Cloud Storage | Auto-uploaded to GCS bucket with public URL generation |
| **Concurrency** | ThreadPoolExecutor | Up to 30 parallel thumbnail fetches |
| **Reliability** | Retry loop (up to 10x) | Keeps retrying failed thumbnails until all are resolved |

Supports unique ID extraction from Instagram, Facebook reels, Facebook share links, posts with `pfbid`, and `watch?v=` URLs.

</details>

<details>
<summary><b>📋 Google Sheets Engine</b> — <code>google_api.py</code></summary>

<br/>

**Full read/write pipeline with professional formatting:**

- **Authentication** via Google Service Account (OAuth2)
- **Read:** Pulls data from any tab in any Google Sheet by ID
- **Write:** Clears existing worksheet (or creates new) and writes DataFrame
- **Formatting via Sheets API batch update:**
  - 🟠 Orange header background (`RGB: 255, 165, 0`)
  - 📝 EB Garamond font (11pt bold headers, 10pt body)
  - 📐 Center-aligned, vertically centered
  - 🖤 Solid black borders on all cells

</details>

---

## ⚡ Parallel Execution

The system processes all four platforms **simultaneously** using Python's `ThreadPoolExecutor`:

```python
with ThreadPoolExecutor() as exe:
    fb_future = exe.submit(run_fb)    # Facebook pipeline
    ig_future = exe.submit(run_ig)    # Instagram pipeline  
    yt_future = exe.submit(run_yt)    # YouTube pipeline
    x_future  = exe.submit(run_x)    # X/Twitter pipeline
```

This means scraping 100 Instagram posts and 50 Facebook posts and 30 YouTube videos takes roughly the **same time as scraping the slowest platform alone** — not the sum of all four.

---

## 📁 Project Structure

```
Link-to-Metrics/
│
├── main.py                          # 🚀 Entry point & orchestrator
├── params.yaml                      # ⚙️ Configuration (API keys, sheet IDs)
│
├── components/
│   ├── function_influencer.py       # 🔀 Platform router & parallel executor
│   ├── function_ig.py               # 📸 Instagram scraping (Apify + BrightData)
│   ├── functions_fb.py              # 📘 Facebook scraping (BrightData + Supermetrics)
│   ├── function_yt.py               # 🎬 YouTube metrics (YouTube Data API v3)
│   ├── function_x.py                # 🐦 X/Twitter scraping (Apify)
│   ├── fb_ig_thumbnail.py           # 🖼️ Thumbnail extraction, compression & GCS upload
│   └── google_api.py                # 📋 Google Sheets read/write with formatting
│
└── pictures/                        # 📂 Temp folder for thumbnail compression
```

---

## 🛠️ Tech Stack

<div align="center">

| Category | Technologies |
|:---|:---|
| **Language** | ![Python](https://img.shields.io/badge/Python_3.10+-3776AB?style=flat-square&logo=python&logoColor=white) |
| **Data Processing** | ![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white) ![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat-square&logo=numpy&logoColor=white) |
| **Web Scraping** | ![BrightData](https://img.shields.io/badge/BrightData_API-FF6B00?style=flat-square) ![Apify](https://img.shields.io/badge/Apify-00C48E?style=flat-square) ![Playwright](https://img.shields.io/badge/Playwright-2EAD33?style=flat-square&logo=playwright&logoColor=white) ![BeautifulSoup](https://img.shields.io/badge/BeautifulSoup4-333?style=flat-square) |
| **APIs** | ![YouTube](https://img.shields.io/badge/YouTube_Data_API_v3-FF0000?style=flat-square&logo=youtube&logoColor=white) ![Supermetrics](https://img.shields.io/badge/Supermetrics-3C5A99?style=flat-square) ![Google Sheets](https://img.shields.io/badge/Google_Sheets_API-34A853?style=flat-square&logo=googlesheets&logoColor=white) |
| **Cloud** | ![GCS](https://img.shields.io/badge/Google_Cloud_Storage-4285F4?style=flat-square&logo=googlecloud&logoColor=white) |
| **Image Processing** | ![Pillow](https://img.shields.io/badge/Pillow_(PIL)-FFD43B?style=flat-square) |
| **Concurrency** | ![Threading](https://img.shields.io/badge/ThreadPoolExecutor-Parallel-blue?style=flat-square) |
| **Auth** | ![OAuth2](https://img.shields.io/badge/OAuth2-Service_Account-orange?style=flat-square) |
| **Config** | ![YAML](https://img.shields.io/badge/YAML-CB171E?style=flat-square&logo=yaml&logoColor=white) |

</div>

---

## 🔐 Configuration

The system is configured via `params.yaml`:

```yaml
bigquery_client_path:     # Path to Google Service Account JSON
supermetrics_api:         # Supermetrics API key (FB shares)
brightdata_api:           # BrightData API token (IG/FB scraping)
apify_api:                # Apify API token (IG/X scraping)

input_output_sheet_id:    # Google Sheet ID for input/output
in_tab_name:              # Input tab name (contains post URLs)
out_tab_name:             # Output tab name (enriched data written here)

inf_cols:                 # Output column schema
```

---

## 🚀 How to Run

```bash
# 1. Clone the repo (private — team access only)
git clone <private-repo-url>

# 2. Install dependencies
pip install pandas gspread gspread-dataframe gspread-formatting \
            oauth2client apify-client requests pyyaml pillow \
            playwright beautifulsoup4 google-cloud-storage \
            google-cloud-bigquery tqdm numpy

# 3. Install Playwright browsers
playwright install chromium

# 4. Configure params.yaml with your API keys & Sheet ID

# 5. Add post URLs to your Google Sheet input tab

# 6. Run
python main.py
```

---

## 📈 Performance

| Metric | Value |
|:---|:---|
| **Platforms supported** | 4 (Instagram, Facebook, YouTube, X) |
| **Parallel execution** | ✅ All platforms scraped simultaneously |
| **Thumbnail retry logic** | Up to 10 automatic retries |
| **Scraping retry** | 2-pass system (failed URLs re-queued) |
| **Concurrent thumbnails** | 30 parallel workers |
| **Output formatting** | Auto-formatted Google Sheets |

---

## 🧩 Data Flow Summary

```
📋 Google Sheet          →  Read post URLs
    ↓
🔍 URL Detection         →  Classify: IG / FB / YT / X
    ↓
⚡ Parallel Scraping     →  All 4 platforms at once
    ↓
📊 Metrics Extraction    →  Likes, Comments, Shares, Views, Followers...
    ↓
🖼️ Thumbnail Pipeline   →  Extract → Compress → Upload to GCS
    ↓
🔗 Merge & Enrich       →  Combine all results into single DataFrame
    ↓
📋 Google Sheet          →  Write formatted output (orange headers, borders)
```

---

## 🤝 Built By

<div align="center">

**[Shanskar Bansal](https://github.com/shanskarBansal)**  
Senior Consultant & Data Scientist  
[Varahe Analytics Pvt. Ltd.](https://varaheanalytics.com)

[![GitHub](https://img.shields.io/badge/GitHub-shanskarBansal-181717?style=for-the-badge&logo=github)](https://github.com/shanskarBansal)
[![Portfolio](https://img.shields.io/badge/Portfolio-shanskarbansal.com-00C7B7?style=for-the-badge&logo=netlify&logoColor=white)](https://shanskarbansal.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/shanskarbansal)

</div>

---

<div align="center">

### ⚠️ Source Code Notice

> This is a **showcase repository** — documentation and architecture only.  
> The source code is proprietary and maintained in a **private repository** at Varahe Analytics.  
> If you're interested in the platform or have questions, feel free to [reach out](mailto:shanskarbansal@gmail.com).

<br/>

⭐ **Star this repo** if you find the architecture interesting!

</div>

