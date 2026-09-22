# FUELICIENT

**HackUTD 2024 — "Ripple Effect" Track**

### Authors
Isaac Philo, Aman Balam, Adrian Alvarez, Sriram Lakamsani

---

**Live site:** [choose.fuelicient.tech](https://isaacphilo.github.io/HackUTD2024/) · [GitHub Pages mirror](https://isaacphilo.github.io/HackUTD2024/)

> Explore. Organize. Remember.

Fuel efficiency data is scattered across dense government spreadsheets and is easy to lose track of. FUELICIENT turns EPA fuel economy data into a clean, persistent, interactive dashboard, so insights don't have to be rediscovered every time.

## Overview

FUELICIENT is a Streamlit web app that lets users explore EPA Fuel Economy Guide data (model years 2021–2025) for Toyota vehicles. Users sign in with an account, choose one or more model years and any combination of data columns, and get instant visualizations — scatter plots and bar charts — comparing fuel efficiency, engine displacement, and CO2 emissions across carlines. Each user's last-used year and column selections are saved to a database and restored automatically the next time they log in.

## Features

- **Account-based sessions** — sign up or log in with email/password (Firebase Authentication), so preferences follow the user across visits.
- **Multi-year data selection** — pull and combine EPA Fuel Economy Guide data for 2021–2025 in a single view.
- **Custom column comparisons** — pick any two numeric columns for a scatter plot, or more for a bar chart of averages.
- **Guided analysis view** — a dedicated dashboard highlighting the top 10 most fuel-efficient carlines, fuel efficiency vs. engine displacement, and city vs. highway CO2 emissions by carline, with adjustable filters (fuel efficiency range, specific carlines).
- **Summary statistics** — mean, median, and standard deviation of fuel efficiency for the current filtered selection.
- **CSV export** — download the filtered dataset for offline use.
- **Persistent preferences** — selected years and columns are saved per user in Firebase Realtime Database and reloaded on the next session.

## How it works

1. The underlying EPA Fuel Economy Guide datasets (`.xlsx`, one per model year) are pinned to IPFS via [Pinata](https://www.pinata.cloud/), and referenced in the app by their content identifier (CID).
2. When a user selects a year, the app fetches the corresponding file straight from the Pinata IPFS gateway, loads it into a pandas DataFrame, and filters it down to Toyota vehicles.
3. The cleaned data is cached in the Streamlit session so repeat requests for the same year don't re-fetch it.
4. Matplotlib/Seaborn charts are rendered inline based on the user's column or filter selections.
5. Firebase (via Pyrebase) handles authentication and stores each user's last-selected years/columns in the Realtime Database.

## Tech stack

| Layer | Technology |
|---|---|
| App framework | [Streamlit](https://streamlit.io/) |
| Data processing | pandas, NumPy |
| Visualization | Matplotlib, Seaborn, Altair |
| Auth & storage | Firebase Authentication + Realtime Database (via Pyrebase4) |
| Data hosting | IPFS, pinned/managed with [Pinata](https://www.pinata.cloud/) (Node.js SDK) |
| Source data | [EPA Fuel Economy Guide](https://www.fueleconomy.gov/feg/download.shtml), model years 2021–2025 |

## Project structure

```
.
├── project.py              # Streamlit entry point: auth flow + app bootstrap
├── lib/
│   ├── main.py              # Core app logic: data fetch/filter, visualizations, Firebase-backed prefs
│   ├── data_fetcher.py      # IPFS/Pinata data retrieval + cleaning helpers
│   ├── methods.py           # Analysis helpers (e.g., most fuel-efficient car lookup)
│   └── visualization.py     # Chart-rendering helpers
├── index.js                 # Node script for listing/managing files pinned to Pinata
├── Data/                    # Local copies of the EPA Fuel Economy Guide datasets (by year)
├── docs/                    # Static site published via GitHub Pages
├── requirements.txt         # Python dependencies
├── package.json             # Node dependencies (dotenv, pinata)
└── .streamlit/config.toml   # Streamlit theme config
```

## Getting started

### Prerequisites

- Python 3.11+
- Node.js (only needed if you're managing Pinata-pinned files via `index.js`)
- A Firebase project with Authentication (email/password) and Realtime Database enabled
- A [Pinata](https://www.pinata.cloud/) account for IPFS-hosted datasets

### Setup

1. Clone the repo and install Python dependencies:
   ```bash
   git clone https://github.com/SriramL1/HackUTD2024.git
   cd HackUTD2024
   pip install -r requirements.txt
   ```
2. (Optional) install Node dependencies if you'll use the Pinata file listing script:
   ```bash
   npm install
   ```
3. Create a `.env` file in the project root with the following variables:
   ```
   PINATA_JWT=
   PINATA_API_KEY=
   PINATA_API_SECRET=
   PINATA_GATEWAY=
   FE_2021=   # IPFS CID for the 2021 Fuel Economy Guide dataset
   FE_2022=   # IPFS CID for the 2022 Fuel Economy Guide dataset
   FE_2023=   # IPFS CID for the 2023 Fuel Economy Guide dataset
   FE_2024=   # IPFS CID for the 2024 Fuel Economy Guide dataset
   FE_2025=   # IPFS CID for the 2025 Fuel Economy Guide dataset
   ```
4. Configure your own Firebase project credentials in `project.py` (the `firebaseConfig` block), pointing to a Realtime Database instance.

### Run the app

```bash
streamlit run project.py
```

The app will be available at `http://localhost:8501`. Sign up with an email/password to create an account, then select one or more model years to start exploring the data.

### Managing pinned datasets (optional)

`index.js` lists files currently pinned to your Pinata account:

```bash
node index.js
```

## Data source

Fuel economy figures come from the U.S. EPA's [Fuel Economy Guide](https://www.fueleconomy.gov/feg/download.shtml) datasets for model years 2021–2025, filtered to Toyota vehicles (`Mfr Name` contains "TOYOTA").

## Open-source libraries used

altair, attrs, blinker, cachetools, certifi, charset-normalizer, click, colorama, contourpy, cycler, et_xmlfile, fonttools, gcloud, gitdb, GitPython, googleapis-common-protos, httplib2, idna, Jinja2, jsonschema, jsonschema-specifications, jws, kiwisolver, markdown-it-py, MarkupSafe, matplotlib, mdurl, narwhals, numpy, oauth2client, openpyxl, packaging, pandas, pillow, protobuf, pyarrow, pyasn1, pyasn1_modules, pycryptodome, pydeck, Pygments, pyparsing, Pyrebase4, python-dateutil, python-dotenv, python-jwt, pytz, referencing, requests, requests-toolbelt, rich, rpds-py, rsa, seaborn, setuptools, six, smmap, streamlit, tenacity, toml, tornado, typing_extensions, tzdata, urllib3, watchdog

(Node.js side: `dotenv`, `pinata`)