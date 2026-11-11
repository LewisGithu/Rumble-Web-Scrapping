# 📊 Rumble Channel Analytics: End-to-End Data Engineering & BI Pipeline

An end-to-end data engineering and analytics project extracting livestream performance data from **TateSpeech** on Rumble, processing it through a **Medallion Architecture (Bronze → Silver → Gold)** in **Databricks**, and delivering actionable creator insights via an interactive **Databricks Dashboard**.

---

## 🏗️ Pipeline Architecture & Workflow

### 1. Automated Web Scraping & DOM Extraction (VS Code + Python + Playwright)
Rumble relies on dynamic JavaScript rendering and anti-bot firewalls that block standard HTTP requests. To overcome this, I built a two-stage local ingestion pipeline in **VS Code**:
* **Headless Browser Staging:** Utilized **Playwright** (with isolated Windows `asyncio` event loop handling) to simulate genuine desktop browsing sessions across 8 paginated livestream pages, archiving the fully rendered DOM snapshots into [`pages_html/`](./pages_html).
* **Custom Web Component Parsing:** Used **BeautifulSoup** to query HTML5 custom web components (`<rum-video-thumbnail>`) and visual thumbnail badges (`rum-video-thumbnail__badge--duration`) directly from [`codes/web_scrap.ipynb`](./codes/web_scrap.ipynb), extracting exact video titles, view counts, upload timestamps, video durations, and direct stream URLs into [`a csv`](./files/tate_speech_livestreams_updated.csv).

### 2. Cloud ETL & Medallion Architecture (Databricks)
The raw extracted datasets were ingested into **Databricks** and transformed across three structured data layers:
* **🥉 Bronze Layer (Raw Ingestion):** Loaded [`the scraped CSV records`](./files/tate_speech_livestreams_updated.csv) into raw Delta tables, preserving original schema attributes, timestamps, and metadata strings for full auditability.
* **🥈 Silver Layer (Cleansing & AI Classification):** Standardized date/time formats, converted string metrics and duration timestamps into clean numerical fields, deduplicated records, and classified livestream topics and content themes.
* **🥇 Gold Layer (Business-Level Aggregations):** Modeled curated [` analytical table`](./files/gold_tate_speech_livestreams.csv) tracking cumulative views, publishing frequency, duration impact on engagement, and top-performing stream series.

### 3. Business Intelligence & Visualization (Databricks Dashboard)
Using the curated [`Gold table`](./files/gold_tate_speech_livestreams.csv), I built an executive dashboard directly inside **Databricks** to uncover channel growth trends, optimal stream lengths, and audience engagement patterns. 
* 📄 **View the Full Dashboard Report:** [`dashboard.pdf`](./dashboard.pdf)

---

## 📂 Repository Structure

```text
Rumble-Web-Scrapping/
├── codes/
│   └── web_scrap.ipynb            # Playwright HTML downloader & BeautifulSoup component parser
│    └──TateSpeech_bronze_to_gold  # Cleaning, parsing, classifying and analysing the data
├── pages_html/                    # Raw archived HTML snapshots (Page 1 to Page 8)
├── files/                         # Extracted & cleaned CSV datasets
├── dashboard.pdf                  # Exported Databricks analytical dashboard
└── README.md                      # Project architecture and documentation
```

---

## 🛠️ Tech Stack
* **Web Scraping & Extraction:** Python, Playwright, BeautifulSoup4, Pandas, VS Code
* **Data Engineering & Storage:** Databricks, Delta Lake (Bronze/Silver/Gold Medallion Architecture), PySpark, Databricks SQL
* **Data Visualization:** Databricks AI/BI Dashboards

---

## 🚀 How to Run Locally
1. Clone the repository:
   ```bash
   git clone https://github.com
   ```
2. Install the required dependencies:
   ```bash
   pip install playwright beautifulsoup4 pandas
   python -m playwright install chrome
   ```
3. Open [`codes/web_scrap.ipynb`](./codes/web_scrap.ipynb) in VS Code to run the Playwright page downloader or parse the cached files inside [`pages_html/`](./pages_html).
