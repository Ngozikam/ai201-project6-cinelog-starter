# PR Response

Thank you for the review feedback. I addressed each comment by following the existing patterns in the CineLog codebase and considering the design tradeoffs specific to the application.

## Comment 1: Watchlist Function Naming

**Response:** Renamed `save_to_watchlist()` to `add_to_watchlist()` and updated all imports and call sites.

**Reasoning:** The existing collection service uses the `add_to_collection()` naming pattern. Renaming the watchlist function to `add_to_watchlist()` follows CineLog's existing `verb_to_noun` convention and keeps the service APIs consistent.

## Comment 2: Duplicate Watchlist Entries

**Response:** Added a duplicate check to `add_to_watchlist()` and introduced `AlreadyInWatchlistError`.

**Reasoning:** I followed the existing deduplication pattern in `add_to_collection()`. Before creating a new `WatchlistEntry`, the service checks for an existing entry with the same `user_id` and `film_id`. If one exists, it raises a specific exception instead of creating a duplicate. I also added a test that verifies the exception is raised and confirms that only one entry remains in the database.

## Comment 3: Watchlist Sort Order

**Response:** Changed the watchlist ordering from alphabetical order to newest-added-first.

**Reasoning:** I agree that recently added films are more useful as the default view for a watchlist. This change also makes `get_watchlist()` consistent with the existing `get_collection()` behavior, which already sorts entries by `date_added` in descending order. Using the same default ordering pattern makes CineLog's collection and watchlist services more predictable and consistent.

## Comment 4: Nonexistent Film Test

**Response:** Added a test verifying that `add_to_watchlist()` raises `FilmNotFoundError` when the requested `film_id` does not exist.

**Reasoning:** I followed the test structure in `test_collection.py` by using the existing fixtures, running the service call inside the application context, and verifying the expected exception with `pytest.raises()`. This ensures invalid film IDs are handled by the service rather than causing a database integrity error.

## Comment 5: Default Watchlist Visibility

**Response:** I decided to keep `public=True` as the default visibility for watchlists.

**Reasoning:** CineLog is described as a community film-tracking application, so public watchlists support the application's social and discovery-oriented purpose by making film interests shareable by default. I recognize the privacy tradeoff: some users may prefer their saved films to remain private. A future improvement would be to expose visibility as an explicit option when adding a film to the watchlist. For the current implementation, I kept the public default because it is consistent with the community-oriented purpose of CineLog while preserving the existing feature behavior.

## Comment 6: Rebase and UUID Migration

**Response:** Pending rebase onto the updated `main` branch.

**Reasoning:** This comment will be addressed after the other review feedback is stable, as recommended in the project instructions.

## Comment 6: Rebase and UUID Migration

**Response:** Rebasing the `feature/watchlist` branch onto the updated `main` branch exposed the film ID migration from integers to UUIDs. I updated the watchlist model to use UUID film IDs and verified the watchlist service against the refactored `Film` model.

**Reasoning:** The updated `main` branch defines `Film.id` as `db.String(36)`, so `WatchlistEntry.film_id` must use the same UUID-compatible type to maintain a valid foreign-key relationship. I preserved the watchlist feature while aligning it with the updated data model. After resolving the rebase changes, I ran the full test suite and confirmed that all six tests pass.