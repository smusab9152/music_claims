# music_claims

┌──────────────────────┐
│        START         │
│  (Main Execution)    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Initialize Spotify  │
│  (Auth Check)        │
└──────────┬───────────┘
           │
           ▼
┌──────────┴──────────┐
│  Decision: Success? │
└──────────┬──────────┘
           ├────────────► No ─► (Exit: Error)
           ▼ Yes
┌──────────────────────┐
│  get_artist_catalog  │
│  (Data Retrieval)    │
└──────────┬───────────┘
           │
           ▼
  [Phase 2: Data Retrieval]
┌─────────────────────────────────────────────────────────────┐
│  REQUEST 1: Fetch first batch of Albums/Singles (Limit 50)  │
└──────────┬──────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────┐
│ LOOP A (Album Pagination): WHILE results.get('next')     │
│   Append releases to 'albums' list.                      │
└──────────────────────────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────┐
│ LOOP B (Album Iteration): FOR each album in 'albums'     │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ REQUEST 2: Fetch first batch of Tracks for this Album│ │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP C (Track Pagination): WHILE tracks_results.next │ │
│ │   Collect all tracks into 'tracks_in_album'.         │ │
│ └──────────────────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ Filter Tracks: Create batch of NEW, UNIQUE Track IDs.│ │
│ │ (Exclude IDs already in 'processed_track_ids' set)   │ │
│ └──────────┬───────────────────────────────────────────┘ │
│            │                                            │
│            ▼                                            │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ LOOP D (Batching/ISRC): FOR batches of 50 Track IDs  │ │
│ │ ┌──────────────────────────────────────────────────┐ │ │
│ │ │ REQUEST 3: Fetch Full Track Details (Contains ISRC)│ │
│ │ └──────────┬───────────────────────────────────────┘ │ │
│ │            │                                        │ │
│ │            ▼                                        │ │
│ │ ┌──────────────────────────────────────────────────┐ │ │
│ │ │ LOOP E (Data Collection): FOR each track result  │ │ │
│ │ │   Extract Data (Name, Album, Date, ISRC)         │ │ │
│ │ │   Append to 'raw_catalog' list                   │ │ │
│ │ │   Add Track ID to 'processed_track_ids' set      │ │ │
│ │ └──────────────────────────────────────────────────┘ │ │
│ └──────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
           │
           ▼
  [Phase 3: Polars Processing]
┌──────────────────────────────────────────────────────────┐
│ Decision: raw_catalog has data?                          │
└──────────┬───────────────────────────────────────────┐
           ▼ Yes                                       │ No
┌──────────────────────────────────────────────────────┐ └─► (Exit: No Data)
│ Polars DF Creation: pl.DataFrame(raw_catalog)        │
├──────────────────────────────────────────────────────┤
│ Polars Deduplication: df.unique(subset=['Track_ID']) │
├──────────────────────────────────────────────────────┤
│ Polars Transform: df.select().sort('Release_Date')   │
├──────────────────────────────────────────────────────┤
│ Export: df.write_csv(filename)                       │
├──────────────────────────────────────────────────────┤
│ Print Summary and Preview                              │
└──────────┬───────────────────────────────────────────┘
           │
           ▼
┌──────────────────────┐
│         END          │
└──────────────────────┘
