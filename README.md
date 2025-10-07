# Music Claims

A repository for managing music-related claims and potentially tracking royalties or rights.  

```
┌──────────────────────┐
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
│            │                                             │
│            ▼                                             │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP C (Track Pagination): Collect all tracks pages  │ │
│ │   Collect all tracks into 'tracks_in_album'.         │ │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                             │
│            ▼                                             │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ Process: Filter out already 'processed_track_ids'    │ |
│ │ (Ensures ISRC is fetched only once per unique track) │ |
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                             │
│            ▼                                             │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP D (Batching & ISRC): Iterate over batches (50)  │ │
│ │ ┌──────────────────────────────────────────────────┐ │ │
│ │ │ REQUEST 3: Fetch Full Track Details (Contains ISRC)│ │
│ │ └──────────┬───────────────────────────────────────┘ │ │
│ │            │                                         │ │
│ │            ▼                                         │ │
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
```



## Features

- Manage music claims (details to be filled in as repository grows)
- Track rights and possibly royalties (expand as implementation progresses)
- Open for contributions and improvements

## Installation

Clone the repository:
```bash
git clone https://github.com/smusab9152/music_claims.git
cd music_claims
```
Install dependencies (add specific instructions for your tech stack here):

```bash
# For example, if using Python:
pip install -r requirements.txt

# Or for Node.js:
npm install
```

## Usage

Provide instructions or examples for running or using the project:

```bash
# Example for running a main script:
python main.py
```
*(Replace with actual usage instructions as code is added)*

## Configuration

Document any required environment variables or configuration files here.

- Example: `DATABASE_URL`, `API_KEY`, etc.
- Describe how to set up the configuration for development and production environments.

## Contributing
Contributions are welcome!  
- MIT License

