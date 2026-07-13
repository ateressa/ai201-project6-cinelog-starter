# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` so it follows the same `add_to_<noun>` naming convention as `collection_service.add_to_collection()`. Updated the one call site in `routes/watchlist/watchlist.py` (the import and the invocation inside `add_film()`).
**How I verified:** Grepped the whole project for `save_to_watchlist` after the rename to confirm no remaining references to the old name.

## Comment 2 — Deduplication
**What I did:** Added an `AlreadyInWatchlistError` exception and a duplicate check in `add_to_watchlist()` (`services/watchlist_service.py`), mirroring the existing `AlreadyInCollectionError` pattern in `collection_service.add_to_collection()`. Before inserting a new `WatchlistEntry`, the function now queries for an existing `(user_id, film_id)` pair and raises before any write occurs if one is found.
**How I verified:** Manually traced the code path against the `add_to_collection()` reference implementation to confirm the same query-then-raise-before-write order, so no partial/duplicate entry can be committed. 

## Comment 3 — Missing test
**What I did:** Created `tests/test_watchlist.py` with `test_add_to_watchlist_nonexistent_film_raises`, the watchlist equivalent of `test_add_to_collection_nonexistent_film_raises` in `tests/test_collection.py`. It reuses the same `app`/`sample_user` fixture pattern and asserts that calling `add_to_watchlist()` with a nonexistent `film_id` raises `FilmNotFoundError`. Since `Film.id` is still an integer (pre-UUID-refactor) on this branch, the fake id used is an out-of-range integer (`999999`) rather than a UUID string, so the test actually matches the current column type.
**How I verified:** Ran `pytest tests/test_watchlist.py -v` — 1 passed.

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
