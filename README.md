# Music Streaming Platform

An object-oriented domain model of a music streaming service in pure Python, with an
analytics layer that answers questions about listening behaviour. Built for the
Programming II course at IMC Krems.

![Class diagram](StreamingPlatform.png)

## What it models

- **Tracks**: an abstract `Track` with songs (`AlbumTrack`, `SingleRelease`), podcasts
  (`InterviewEpisode`, `NarrativeEpisode`) and `AudiobookTrack`
- **Users**: `FreeUser`, `PremiumUser`, and `FamilyAccountUser` with `FamilyMember` sub-accounts
- **Content**: `Artist`, `Album` (with track ordering), `Playlist` and `CollaborativePlaylist`
- **Activity**: `ListeningSession` records who listened to what, when, and for how long
- **`StreamingPlatform`**: the registry that ties everything together and runs the queries

## Analytics queries

`StreamingPlatform` implements ten queries over the recorded sessions:

| # | Method | Answers |
|---|--------|---------|
| 1 | `total_listening_time_minutes(start, end)` | Total listening time in a time window |
| 2 | `avg_unique_tracks_per_premium_user(days)` | Average distinct tracks per premium user recently |
| 3 | `track_with_most_distinct_listeners()` | The track with the widest audience |
| 4 | `avg_session_duration_by_user_type()` | Session length ranked by user tier |
| 5 | `total_listening_time_underage_sub_users_minutes(age)` | Listening time of underage family members |
| 6 | `top_artists_by_listening_time(n)` | Top artists by minutes listened (songs only) |
| 7 | `user_top_genre(user_id)` | A user's favourite genre and its share of their time |
| 8 | `collaborative_playlists_with_many_artists(threshold)` | Collaborative playlists spanning many artists |
| 9 | `avg_tracks_per_playlist_type()` | Average playlist size, standard vs. collaborative |
| 10 | `users_who_completed_albums()` | Users who listened to every track of an album |

## Example

```python
from datetime import date, datetime
from streaming.platform import StreamingPlatform
from streaming.artists import Artist
from streaming.tracks import SingleRelease
from streaming.users import FreeUser
from streaming.sessions import ListeningSession

platform = StreamingPlatform("Demo")

artist = Artist("a1", "Pixels", genre="pop")
track = SingleRelease("t1", "Pixel Rain", 180, "pop", artist, release_date=date(2024, 5, 1))
alice = FreeUser("u1", "Alice", age=30)

platform.add_artist(artist)
platform.add_track(track)
platform.add_user(alice)
platform.record_session(ListeningSession("s1", alice, track, datetime.now(), 150))

print(platform.top_artists_by_listening_time(n=1)[0][1])  # 2.5 (minutes)
print(platform.user_top_genre("u1"))                      # ('pop', 100.0)
```

See `tests/conftest.py` for a fully populated platform built from these classes.

## Running the tests

Requires Python 3.12+.

```bash
pip install pytest
pytest
```

The suite has 86 tests: unit tests per class in `tests/unit_tests/` and tests for the
ten queries in `tests/test_public.py`.

## Project layout

```
src/streaming/     domain model and StreamingPlatform
tests/             pytest suite and shared fixtures
ClassDiagram.puml  PlantUML source of the class diagram
```

## Credits

The assignment specification, class diagram, unit tests and test fixtures were provided
by the course. The implementation in `src/streaming/` and the completed query tests in
`tests/test_public.py` are my own work.
