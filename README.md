# Spotify Recommendation Engine

A content-based music recommendation system built on top of your own Spotify listening data. Instead of relying on collaborative filtering (what *other* users liked), this project summarizes a playlist's audio "fingerprint" and finds songs from a larger catalog that match it most closely.

## How it works

1. **Data preparation** — Song-level Spotify data (audio features like tempo, valence, liveness, etc.) is merged with artist-level data (genres).
2. **Feature engineering**
   - Numerical audio features are normalized.
   - `year` and `popularity` are one-hot encoded into buckets.
   - Artist genres are turned into TF-IDF features.
   - Everything is combined into a single feature set per track.
3. **Spotify API connection** — Uses [Spotipy](https://spotipy.readthedocs.io/) to pull your playlists, track metadata, and cover art directly from your Spotify account.
4. **Playlist vector generation** — A playlist is summarized into a single vector representing its overall "sound," with a recency-weighted bias so recently added songs count more.
5. **Recommendation generation** — Cosine similarity between the playlist vector and every non-playlist track in the catalog surfaces the top matching songs.

## Tech stack

- Python, pandas, NumPy
- scikit-learn (`TfidfVectorizer`, `cosine_similarity`, `MinMaxScaler`)
- Spotipy (Spotify Web API client)
- Matplotlib / scikit-image (for visualizing playlist cover art)

## Project structure

```
├── spotify-recommendation-engine.ipynb   # main notebook: data prep, feature engineering, recommendations
├── spotify/
│   ├── data.csv                          # base song-level dataset
│   ├── Folklore.csv
│   ├── Hindie.csv
│   ├── Nepali_Gem.csv
│   ├── Old_Melodies.csv
│   ├── Samui.csv
│   └── Southie.csv                       # exported playlists used to test recommendations
```

## Setup

1. Clone the repo and install dependencies:
   ```bash
   pip install pandas numpy scikit-learn matplotlib scikit-image spotipy
   ```
2. Create a [Spotify Developer app](https://developer.spotify.com/dashboard/) to get a Client ID and Secret.
3. Set up authentication in the notebook using `SpotifyOAuth` (playlist read access is required).
4. Run the notebook top to bottom — it will pull your playlists, build the feature set, and generate recommendations.

## Usage

Point the engine at any playlist in your account, and it returns a ranked list of tracks (with cover art) from the wider catalog that best match that playlist's overall vibe — audio characteristics and genre combined.

## Notes

- This is an **offline, content-based** recommender — it works from audio/genre features rather than crowd behavior, so it works even for niche or small playlists with no other listeners.
- The notebook includes an example EDM-style recommendation flow that can be adapted to any playlist by changing the playlist name.
