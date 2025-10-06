Spotify Artist Catalog Exporter (Polars)🎶 OverviewThis Python script is designed to retrieve the complete discography for a specified artist from the Spotify Web API. It efficiently handles data complexity by performing two levels of pagination (for albums and tracks) and making batched API calls to retrieve the crucial ISRC (International Standard Recording Code) for every unique track.The final dataset is cleaned, deduplicated, and transformed using the high-performance Polars data frame library before being exported to a CSV file.PrerequisitesTo run this script, you need:Python 3.8+Required Libraries:pip install spotipy polars
Spotify API Credentials: A CLIENT_ID and CLIENT_SECRET obtained by creating an application in the [Spotify Developer Dashboard].🏗️ Data Retrieval WorkflowThe code logic is highly structured to manage the nested nature of the Spotify API's data structure (Artist → Album/Release → Track).The overall process is a three-phase pipeline: Initialization, Retrieval, and Transformation.Codelogic DiagramThe key to this pipeline is the transition from broad releases to individual tracks and the necessary second API call (Request 3) to fetch the ISRC.┌──────────────────────┐
│        START         │
│  (Setup & Auth)      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Initialize Spotipy  │
│  (Get Access Token)  │
└──────────┬───────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────────┐
│ LOOP A (Album Pagination): Fetch All Releases (Albums/Singles)
└──────────┬──────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────┐
│ LOOP B (Album Iteration): FOR each unique release found  │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ REQUEST 2: Fetch first batch of Tracks for this Album│ │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP C (Track Pagination): Collect all tracks pages  │ │
│ │   Collect all tracks into 'tracks_in_album'.         │ │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ Process: Filter out already 'processed_track_ids'    │
│ │ (Ensures ISRC is fetched only once per unique track) │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP D (Batching & ISRC): Iterate over batches (50)  │ │
│ │ ┌──────────────────────────────────────────────────┐ │ │
│ │ │ REQUEST 3: Fetch Full Track Details (Contains ISRC)│ │
│ │ └──────────┬───────────────────────────────────────┘ │ │
│ │            │                                        │ │
│ │            ▼                                        │ │
│ │ ┌──────────────────────────────────────────────────┐ │ │
│ │ │ LOOP E (Data Collection): Process track results  │ │ │
│ │ │   Extract Data & Append to 'raw_catalog'         │ │ │
│ │ │   Add Track ID to 'processed_track_ids' set      │ │ │
│ │ └──────────────────────────────────────────────────┘ │ │
│ └──────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────┐
│ Polars Transformation and Cleaning                   │
│ 1. Create Polars DF: pl.DataFrame(raw_catalog)       │
│ 2. Deduplicate: df.unique(subset=['Track_ID'])       │
│ 3. Sort: df.sort('Release_Date', descending=True)    │
├──────────────────────────────────────────────────────┤
│ Export: df.write_csv(filename)                       │
└──────────┬───────────────────────────────────────────┘
           │
           ▼
┌──────────────────────┐
│         END          │
└──────────────────────┘

---

## 🛠️ Usage

1.  **Configure Credentials:** Open the script and replace the placeholder values for `CLIENT_ID`, `CLIENT_SECRET`, and define the `ARTIST_ID` you wish to analyze.

    ```python
    CLIENT_ID = 'YOUR_CLIENT_ID'
    CLIENT_SECRET = 'YOUR_CLIENT_SECRET'
    ARTIST_ID = '4Z8W4fKeB5YxbusRsdQVPb' # Example: Radiohead
    ```

2.  **Run the Script:**

    ```bash
    python full_spotify_catalog_polars_simplified.py
    ```

3.  **Check Output:** The script will generate a file named after the artist (e.g., `Radiohead_Full_Catalog_Simplified.csv`) containing the final, clean catalog data.
