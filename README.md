# BBox-The-Tabletop-Music-Controller
BBox is a compact tabletop music controller that uses Mopidy and ESP32-S3 to provide a high-quality music experience with Bluetooth connectivity, audio controls, and a visual audio visualizer. The project leverages Mopidy as the backend to stream music from YouTube, manage playlists, and display album art.
Features
-Bluetooth Audio Streaming: Connects to a DIY speaker for continuous music playback.
-Physical Controls: Includes buttons for play/pause, skip, and a rotary encoder for volume control.
-Audio Visualizer: LED light bars jump in sync with the music.
-Web Interface: Uses Mopidy's MusicBox web client to browse, queue, and control music.
-YouTube Integration: Streams music from YouTube using the YouTube Data API.

Setup Steps
1. Install Mopidy and Set Up Backend on WSL
Install WSL: Enabled Windows Subsystem for Linux to provide a stable environment for Mopidy.
Install Mopidy: "sudo apt update
sudo apt install -y mopidy python3-gi python3-pip gstreamer1.0-plugins-{base,good,bad,ugly} gstreamer1.0-libav"

Configure Mopidy:
Installed Mopidy-MusicBox-Webclient to provide a web interface for controlling playback.
Set up HTTP server access on http://localhost:6680/.

2. YouTube Integration
YouTube API Key:
Created a project in Google Cloud Console and enabled the YouTube Data API v3.
Added API key to Mopidy’s configuration file:
"[youtube]
enabled = true
api_key = YOUR_YOUTUBE_API_KEY"

3.Optimizations for Efficiency in Mopidy Configuration
Why these optimizations were necessary:
Using the YouTube Data API with Mopidy provides access to a vast music library, but YouTube imposes a daily quota limit for API requests. To avoid exceeding this limit (which could lead to service interruptions) and to ensure that BBox runs smoothly on limited hardware, we implemented several optimizations. These adjustments were carefully chosen to balance efficient API usage with maintaining a high-quality user experience.
a. Cache Setup
What we did: Configured a cache directory for Mopidy in the [core] section of the configuration file:
"[core]
cache_dir = ~/.cache/mopidy"
How we did it: Specified a cache directory in the Mopidy configuration to store temporary data, such as search results and metadata.
Why this helps: Caching reduces the number of repeated API requests by storing data locally. For example, if a user searches for the same song multiple times, Mopidy retrieves the results from the cache rather than querying YouTube again, saving API requests. This also improves response time, as cached data loads faster than requesting it from YouTube each time.

b.Timeouts and Metadata Limits for Stream Handling
What we did: Adjusted metadata timeouts and optimized stream settings in the [stream] section:
"[stream]
timeout = 3000  # Set timeout to 3000ms for faster handling
metadata_blacklist =  # Can specify to ignore certain sources if needed"
How we did it: Set a timeout value for streams and metadata requests, specifying how long Mopidy should wait for a response before moving on. Setting a reasonable timeout (e.g., 3000 milliseconds) prevents Mopidy from hanging on slow or non-responsive streams.
Why this helps: This prevents the system from getting bogged down if a particular song or stream takes too long to load. It improves overall user experience by ensuring that users don’t have to wait too long for their songs to play or for album metadata to load. It also avoids consuming unnecessary resources on stalled requests.

c.Reducing Logging Level
What we did: Reduced verbosity in the [logging] section to only essential information:
"[logging]
verbosity = 1  # Lower verbosity for essential logging only"
How we did it: Set the logging level to 1, which restricts Mopidy’s logging output to only critical information.
Why this helps: Reducing logging saves system resources by minimizing the amount of data Mopidy has to write to logs. While verbose logging can be useful for debugging, it’s unnecessary for everyday use and can slow down the system. With lower verbosity, BBox can dedicate more resources to streaming and processing audio rather than logging minor events.

d.Limiting Tracklist Length
What we did: Set a limit for the maximum number of tracks Mopidy can load in a tracklist:
"[core]
max_tracklist_length = 500  # Set a reasonable cap for tracklist size"
How we did it: Defined a max_tracklist_length of 500, which restricts the number of tracks that can be loaded into the playlist or queue at one time.
Why this helps: Large playlists consume more memory and can slow down the system, especially on lower-powered hardware. Limiting the tracklist length keeps Mopidy running efficiently, prevents crashes, and ensures a smoother user experience. For personal use, a limit of 500 tracks is more than sufficient.
