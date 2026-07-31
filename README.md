# Spotify Recommendation Engine

An offline, content-based music recommender built on top of a Kaggle Spotify dataset and the Spotify Web API. Given one of your playlists, it learns a "taste vector" from the songs in it and recommends the most similar tracks from the wider catalog.

## How it works

1. **Data prep** — Loads two datasets: song-level audio features (`data.csv`) and artist-level genre info (`data_w_genres.csv`). Cleans messy string-encoded artist/genre columns, deduplicates songs, and merges genre data onto each song via an explode → merge → re-aggregate step.
2. **Feature engineering** — Builds one numeric vector per song from three weighted signals:
   - Genres → TF-IDF
   - Release year & popularity → one-hot encoded, bucketed
   - Audio features (danceability, energy, valence, tempo, etc.) → min-max scaled
3. **Spotify API connection** — Authenticates via `spotipy` (OAuth) to pull your real playlists.
4. **Playlist vector** — Averages the feature vectors of songs in a chosen playlist into a single vector, with recency weighting so recently added songs count more.
5. **Recommendations** — Computes cosine similarity between the playlist vector and every other song in the catalog, returning the top 40 matches, with album art visualization.

## Setup

Requires Python 3.9+ and a Spotify Developer app.

```bash
pip install pandas numpy scikit-learn matplotlib spotipy scikit-image
```

Set your Spotify credentials as environment variables (never hardcode them):

```bash
export SPOTIPY_CLIENT_ID="your-client-id"
export SPOTIPY_CLIENT_SECRET="your-client-secret"
export SPOTIPY_USERNAME="your-spotify-username"
```

In your [Spotify Developer Dashboard](https://developer.spotify.com/dashboard), add this exact redirect URI to your app's settings:

```
http://127.0.0.1:8881/
```

## Data

Datasets sourced from the [Music Recommendation System using Spotify Dataset](https://www.kaggle.com/code/vatsalmavani/music-recommendation-system-using-spotify-dataset) on Kaggle. Place `data.csv` and `data_w_genres.csv` in a `data/` folder alongside the notebook.

## Usage

Run the notebook top to bottom. It will:
1. Build the full song feature set from the Kaggle data
2. Pull your Spotify playlists
3. Generate a taste vector for a chosen playlist (currently wired up for an **EDM** playlist)
4. Output and visualize the top 40 recommended tracks

To recommend from a different playlist, uncomment and adapt the relevant lines near the bottom of the notebook (e.g. the `chill` playlist block), pointing them at a playlist that exists in your account.

## Known limitations

- Only tested end-to-end against one playlist (EDM); other playlist blocks are scaffolded but commented out.
- Feature weights (genre, year, popularity, audio features) are manually tuned constants, not learned.
- Requires the song to exist in the static Kaggle dataset to be eligible for recommendation — brand-new releases won't show up.
