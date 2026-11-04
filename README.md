# Spotify Data Analysis: Do Hit Songs Lift the Rest of the Album?

A statistical analysis of 114,000 Spotify tracks in Python. It asks two questions: whether the other tracks on an album with a hit are more popular than tracks on albums without one, and how solo and collaborative tracks differ musically.

This was the final team project for York University's MATH 1130 (Data Science) course, completed in December 2024 by a team of five led by Krish Patel.

**Headline result:** other tracks on albums with a hit averaged a popularity of **36.7**, versus **24.8** for tracks on albums without one. In a one-sided t-test, t = 41.03 and p < 0.001.

## Research questions

1. Is the average popularity of the other tracks on albums with a highly popular track higher than the average popularity of tracks on albums without one?
2. How do solo and collaborative tracks differ in their musical qualities, and in how those qualities relate to popularity?

## Data

The data is the [Spotify Tracks Dataset](https://huggingface.co/datasets/maharshipandya/spotify-tracks-dataset) by Maharshi Pandya, released under a BSD license. It covers 114,000 tracks across 125 genres. Each track has a popularity score (0–100) and audio features such as danceability, energy, loudness, speechiness, acousticness and valence.

The dataset isn't included in this repo. Download it as `dataset.csv` and place it next to the notebook.

## Approach

**Cleaning (pandas)**
- Dropped the one row missing its artist, album and track name, and removed exact duplicate rows.
- Merged tracks listed under several genres into one row per track, with the genres combined.
- Converted duration from milliseconds to minutes.
- Kept albums with at least two tracks, which left **56,470 tracks**.

**Question 1: hypothesis test**
- Defined a hit as a track above the 80th percentile of popularity, which is a score above 49.
- For albums with a hit, averaged the popularity of the other tracks (scores below 49). For albums without a hit, averaged all tracks.
- Ran a one-sided two-sample t-test (SciPy) on the per-album means, with H₀: μ₁ ≤ μ₂, H₁: μ₁ > μ₂ and α = 0.05.

**Question 2: solo vs. collaborative tracks**
- Marked a track as collaborative when its artist field lists more than one artist.
- Compared the average audio features of the two groups, using grouped bar charts, boxplots and correlation heatmaps (Matplotlib, seaborn).
- Fitted separate linear regressions of popularity on danceability, energy, loudness and speechiness for each group (scikit-learn).

## Results

**Question 1.** Other tracks on albums with a hit averaged 36.7 popularity, versus 24.8 on albums without one (t = 41.03, p < 0.001). The notebook prints p = 0.0 because the value underflows. We reject H₀: having a hit on an album is associated with higher popularity for the rest of it. This is an association, not proof that the hit causes it.

**Question 2.** On average, solo and collaborative tracks sound similar:

| Feature | Solo | Collaborative |
| --- | --- | --- |
| Danceability | 0.541 | 0.574 |
| Energy | 0.641 | 0.601 |
| Loudness (dB) | −8.51 | −8.92 |
| Speechiness | 0.089 | 0.085 |
| Acousticness | 0.326 | 0.364 |
| Valence | 0.471 | 0.465 |
| Popularity | 29.5 | 28.3 |

The relationship with popularity differs, though. Each slope below is the change in popularity across the feature's full 0–1 range, or per dB for loudness:

| Feature | Solo slope | Collaborative slope |
| --- | --- | --- |
| Danceability | 2.63 | 6.39 |
| Energy | 2.30 | 9.96 |
| Loudness | 0.24 | 0.66 |
| Speechiness | −9.06 | 3.29 |

The popularity of collaborative tracks rises much more steeply with energy and danceability. Solo tracks become less popular as they get more speech-heavy.

## Limitations

- The hit threshold (80th percentile) is a modeling choice. Re-running the test at other cutoffs would show how robust the result is.
- Spotify's popularity score also reflects recency, playlist placement and marketing, which the dataset doesn't capture.
- Collaborations are detected from separators in the artist field (`;`, `,`, `&`, `and`). These checks also match some single artists, such as names containing "and" ("Brandon") or band names with "&". As a result, some solo tracks are counted as collaborative.
- Each regression uses a single feature, so the slopes describe associations, not a predictive model.
- Where the report or presentation differ from the notebook, this README uses the notebook's output.

## Presentation

The team's narrated presentation (about 9 minutes) walks through the questions, method and findings: [Spotify_Data_Analysis_Presentation.mp4](presentation/Spotify_Data_Analysis_Presentation.mp4).

## Repository contents

| File | What it is |
| --- | --- |
| `spotify_analysis.ipynb` | Full analysis with outputs and charts |
| `report/Spotify_Data_Analysis_Report.pdf` | Final project report |
| `presentation/Spotify_Data_Analysis_Presentation.mp4` | Narrated project presentation (about 9 minutes) |
| `requirements.txt` | Python packages the notebook imports |

## Run it

The notebook was run with Python 3.12.

```bash
pip install -r requirements.txt
# download dataset.csv (see Data above) into this folder
jupyter notebook spotify_analysis.ipynb
```

## Team

Krish Patel (project lead), Smith Patel, Saumya Patel, Utsav Patel and Om Bagadia.

**Tools:** Python, pandas, NumPy, SciPy, scikit-learn, Matplotlib, seaborn, Jupyter

## License

The code and documents in this repo are released under the [MIT License](LICENSE). The Spotify Tracks Dataset is distributed separately under its own BSD license.
