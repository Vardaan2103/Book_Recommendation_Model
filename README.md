# Book Recommendation System

Three recommendation approaches implemented from scratch and compared side by side: popularity-based, collaborative filtering, and content-based (genre) filtering.

## The three models

### 1. Popularity-based
No personalization — ranks books using the IMDB-style weighted rating formula, so a book with 3 five-star ratings doesn't outrank one with 10,000 ratings averaging 4.5 stars.

### 2. Collaborative filtering (item-based)
Recommends books based on rating *patterns*, not content. Two books are considered similar if the same users tended to rate them similarly. Built on:
- A user × book ratings matrix (restricted to the 1,000 most-rated books, to keep it tractable)
- Per-user standardization, so a generous rater and a harsh critic get treated fairly
- Cosine similarity between books

### 3. Content-based (genre) filtering
Recommends books purely by genre overlap with what the user says they like — no rating history needed. Built on a `(books × genres)` matrix where each book stores its rating in every genre it belongs to.

This matrix is naturally very sparse (~982 possible genres, but any single book only has a handful) — **99.2% of entries are zero**. Storing it as a dense NumPy array cost 394MB; stored as a `scipy.sparse` matrix instead, it's under 1MB with identical results.

## Datasets

- **Book-Crossing dataset** (Books.csv, Ratings.csv, Users.csv) — 271K books, 1.1M ratings, 278K users
- **Best Books Ever dataset** (books_1.Best_Books_Ever.csv) — 52K books scraped from Goodreads, with genre tags

Both are included directly in `data/` — see `data/README.md` for source credits.

## Setup

```bash
git clone https://github.com/Vardaan2103/book-recommendation-system.git
cd book-recommendation-system

python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

Then open and run the notebook top to bottom:

```bash
jupyter notebook notebooks/book_recommendation_model.ipynb
```

## Project structure

```
book-recommendation-system/
├── data/
│   ├── README.md               # Dataset sources/credits
│   ├── Books.csv
│   ├── Ratings.csv
│   ├── Users.csv
│   └── books_1.Best_Books_Ever.csv
├── models/                     # Precomputed artifacts, small enough to commit
│   ├── item_similarity_df.pkl  # Item-item similarity matrix (7.7MB)
│   ├── genre_matrix_sparse.npz # Sparse genre matrix (583KB, was 394MB dense)
│   ├── genres_order.txt        # Genre name → column index mapping
│   └── avg_rating.csv          # Precomputed popularity scores
├── notebooks/
│   └── book_recommendation_model.ipynb
└── requirements.txt
```

## Example results

Given a small rating history for a few Harry Potter and fantasy books, the collaborative filter recommends other bestsellers in similar genres (courtroom thrillers, more fantasy/mystery series) — it's picking up on *audience overlap*, not content similarity.

Given genre preferences like `["Fiction", "Romance", "Magic", "Vampires", "Action"]`, the content-based model returns books tagged with exactly those genres, regardless of popularity or rating history.

## Possible next steps

- Wrap the three models behind a simple Streamlit or Flask interface
- Add a hybrid model that blends collaborative + content-based scores
- Evaluate recommendation quality with train/test splits (precision@k, recall@k)
- Handle cold-start users (no rating history) by falling back to the popularity or content-based model
