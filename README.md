# Tautulli Widgets
## Recently Added

![](recently_added_small.png)
![](recently_added_large.png)

```yml
- type: custom-api
  title: Recently Added
  cache: 5m
  url: http://${TAUTULLI_IP}:${TAUTULLI_PORT}/api/v2?apikey=${TAUTULLI_API_KEY}&cmd=get_recently_added&count=10
  options:
    size: "small" 
    collapse: 5
  template: |
    {{ $isLarge := eq (.Options.StringOr "size" "large") "large" }}
    {{ $serverID := "${SERVER_ID}" }}
    {{ $uniqueID := "recently_added" }}
    {{ $collapse := .Options.IntOr "collapse" 5 }}
    {{ $rows := .JSON.Array "response.data.recently_added" }}

    {{ if $isLarge }}
      <!-- Large View -->
      <div style="display: flex; align-items: center; gap: 0.5rem;">
        <!-- Left Arrow -->
        <div id="scrollLeft-{{ $uniqueID }}" onclick='document.getElementById("scrollContainer-{{ $uniqueID }}").scrollBy({ left: -150, behavior: "smooth" })'
          style="flex: 0 0 2.5rem; height: 100px; display: flex; align-items: center; justify-content: center; background: var(--color-surface); cursor: pointer;">
          <span class="color-primary" style="font-size: 1.5rem; opacity: 0.7;">◀</span>
        </div>

        <!-- Scroll Content -->
        <div id="scrollContainer-{{ $uniqueID }}"
          style="display: flex; overflow-x: auto; gap: 1rem; padding-block: 0.5rem; scroll-behavior: smooth; scrollbar-width: none; -ms-overflow-style: none;">
          {{ range $i, $v := $rows }}
            {{ $type := $v.String "media_type" }}
            {{ $isEpisode := eq $type "episode" }}
            {{ $isMovie := eq $type "movie" }}
            {{ $isSeason := eq $type "season" }}
            {{ $isShow := eq $type "show" }}
            {{ $ratingKey := $v.String "rating_key" }}
            {{ $lastWatch := printf "%d" ($v.Int "added_at") }}

            {{ $img := "" }}
            {{ $title := "" }}
            {{ $sub := "" }}

            {{ if $isEpisode }}
              {{ $img = $v.String "grandparent_thumb" }}
              {{ $title = $v.String "grandparent_title" }}
              {{ $sub = printf "S%d · E%d" ($v.Int "parent_media_index") ($v.Int "media_index") }}
            {{ else if $isMovie }}
              {{ $img = $v.String "thumb" }}
              {{ $title = $v.String "title" }}
              {{ $sub = printf "%d" ($v.Int "year") }}
            {{ else if $isSeason }}
              {{ $img = $v.String "parent_thumb" }}
              {{ $title = $v.String "parent_title" }}
              {{ $sub = $v.String "title" }}
            {{ else if $isShow }}
              {{ $img = $v.String "thumb" }}
              {{ $title = $v.String "title" }}
              {{ $sub = printf "%d" ($v.Int "year") }}
            {{ end }}

            <a href="https://app.plex.tv/desktop/#!/server/{{ $serverID }}/details?key=%2Flibrary%2Fmetadata%2F{{ $ratingKey }}" target="_blank"
              style="min-width: 160px; text-align: center; text-decoration: none;">
              <div style="max-height: 225px; overflow: hidden; border-radius: 6px;">
                <img src="http://${TAUTULLI_IP}:${TAUTULLI_PORT}/api/v2?apikey=${TAUTULLI_API_KEY}&cmd=pms_image_proxy&img={{ $img }}"
                  alt="thumb" title="{{ $title }}"
                  style="width: 100%; height: auto; object-fit: cover;" />
              </div>
              <div class="color-highlight" style="margin-top: 1rem; overflow: hidden; white-space: nowrap; text-overflow: ellipsis;" title="{{ $title }}">
                {{ $title }}
              </div>
              <div class="color-highlight" style="overflow: hidden; white-space: nowrap; text-overflow: ellipsis;">
                {{ $sub }}
              </div>
              <div class="color-primary" style="font-size: 0.9em;">
                <span {{ parseRelativeTime "unix" $lastWatch }}></span> ago
              </div>
            </a>
          {{ end }}
        </div>

        <!-- Right Arrow -->
        <div id="scrollRight-{{ $uniqueID }}"
          onclick='document.getElementById("scrollContainer-{{ $uniqueID }}").scrollBy({ left: 150, behavior: "smooth" })'
          style="flex: 0 0 2.5rem; height: 100px; display: flex; align-items: center; justify-content: center; background: var(--color-surface); cursor: pointer;">
          <span class="color-primary" style="font-size: 1.5rem; opacity: 0.7;">▶</span>
        </div>
      </div>
    {{ else }}
      <!-- Small View -->
      <ul class="list collapsible-container" data-collapse-after="{{ $collapse }}">
        {{ range $i, $v := $rows }}
          {{ $type := $v.String "media_type" }}
          {{ $isEpisode := eq $type "episode" }}
          {{ $isMovie := eq $type "movie" }}
          {{ $isSeason := eq $type "season" }}
          {{ $isShow := eq $type "show" }}
          {{ $ratingKey := $v.String "rating_key" }}
          {{ $lastWatch := printf "%d" ($v.Int "added_at") }}

          {{ $img := "" }}
          {{ $title := "" }}
          {{ $sub := "" }}

          {{ if $isEpisode }}
            {{ $img = $v.String "grandparent_thumb" }}
            {{ $title = $v.String "grandparent_title" }}
            {{ $sub = printf "S%d · E%d" ($v.Int "parent_media_index") ($v.Int "media_index") }}
          {{ else if $isMovie }}
            {{ $img = $v.String "thumb" }}
            {{ $title = $v.String "title" }}
            {{ $sub = printf "%d" ($v.Int "year") }}
          {{ else if $isSeason }}
            {{ $img = $v.String "parent_thumb" }}
            {{ $title = $v.String "parent_title" }}
            {{ $sub = $v.String "title" }}
          {{ else if $isShow }}
            {{ $img = $v.String "thumb" }}
            {{ $title = $v.String "title" }}
            {{ $sub = printf "%d" ($v.Int "year") }}
          {{ end }}

          <li class="flex items-center gap-2" style="padding: 0.5rem 0; border-bottom: 1px solid var(--color-border);">
            <img src="http://${TAUTULLI_IP}:${TAUTULLI_PORT}/api/v2?apikey=${TAUTULLI_API_KEY}&cmd=pms_image_proxy&img={{ $img }}" 
              alt="thumb" style="width: 50px; margin-right: 1.5rem;" class="rounded w-10 h-10 object-cover" />
            <div class="min-width-0 grow">
              <div class="color-highlight" style="overflow: hidden; white-space: nowrap; text-overflow: ellipsis;" title="{{ $title }}">
                {{ $title }}
              </div>
              <div class="color-highlight" style="overflow: hidden; white-space: nowrap; text-overflow: ellipsis;">
                {{ $sub }}
              </div>
              <div class="color-primary">
                <span {{ parseRelativeTime "unix" $lastWatch }}></span> ago
              </div>
            </div>
            <a href="https://app.plex.tv/desktop/#!/server/{{ $serverID }}/details?key=%2Flibrary%2Fmetadata%2F{{ $ratingKey }}" target="_blank"
              class="color-primary">▶</a>
          </li>
        {{ end }}
      </ul>
    {{ end }}
```
### Options
- `size` - Set to "small" for small version, "full" version.
- `collapse` - Set the number of items shown by default in "small" mode before showing 'show more.'

### Environment Variables
- `TAUTULLI_IP` - local ip address of your tautulli instance
- `TAUTULLI_PORT` - port of your tautulli instance
- `TAUTULLI_API_KEY` - API key for your tautulli instance. Can be found in settings -> Web Interface -> API 
- `SERVER_ID` - Unique ID key for your Plex server. This is required for links to your plex to work. Can be found using this URL {TAUTULLI_IP}:{TAUTULLI_PORT}/api/v2?apikey={TAUTULLI_API_KEY}&cmd=get_server_id&hostname={PLEX_IP}&port={PLEX_PORT} under "identifier"

## Now Playing

![](now_playing.png)

```yml
- type: custom-api
  title: " "
  cache: 5m
  url: http://${TAUTULLI_IP}:${TAUTULLI_PORT}/api/v2?apikey=${TAUTULLI_API_KEY}&cmd=get_activity
  template: |
    {{ $sessions := .JSON.Array "response.data.sessions" }}
    {{ $collapse := .Options.IntOr "collapse" 5 }}

    {{ if eq (len $sessions) 0 }}
      <div class="color-dim">No one is watching anything right now.</div>
    {{ else }}
      <ul class="list collapsible-container" data-collapse-after="{{ $collapse }}">
        {{ range $session := $sessions }}
          {{ $type := $session.String "media_type" }}
          {{ $isEpisode := eq $type "episode" }}
          {{ $isMovie := eq $type "movie" }}
          {{ $thumb := "" }}
          {{ $title := "" }}
          {{ $subtitle := "" }}

          {{ if $isEpisode }}
            {{ $title = $session.String "grandparent_title" }}
            {{ $subtitle = printf "S%02dE%02d - %s" ($session.Int "parent_media_index") ($session.Int "media_index") ($session.String "title") }}
            {{ $thumb = $session.String "grandparent_thumb" }}
          {{ else }}
            {{ $title = $session.String "title" }}
            {{ $thumb = $session.String "thumb" }}
          {{ end }}

          {{ $duration := $session.Int "duration" }}
          {{ $offset := $session.Int "view_offset" }}
          {{ $remaining := div (sub $duration $offset) 1000 }}
          {{ $now := now }}
          {{ $endTime := $now.Add (duration (printf "%ds" $remaining)) | formatTime "3:04PM" }}
          {{ $ratingKey := $session.String "rating_key" }}

          <li class="flex items-center gap-2" style="padding: 0.5rem 0; border-bottom: 1px solid var(--color-border);">
            <a href="https://app.plex.tv/desktop/#!/server/${SERVER_ID}/details?key=%2Flibrary%2Fmetadata%2F{{ $ratingKey }}"
              title="{{ $session.String "summary" }}">
              <img
                src="http://${TAUTULLI_IP}:${TAUTULLI_PORT}/api/v2?apikey=${TAUTULLI_API_KEY}&cmd=pms_image_proxy&img={{ $thumb }}"
                alt="thumb"
                style="width: 50px; margin-right: 1.5rem;"
                class="rounded object-cover"
              />
            </a>

            <div class="min-width-0 grow">
              <div class="color-highlight"
                style="overflow: hidden; white-space: nowrap; text-overflow: ellipsis;"
                title="{{ $title }}">
                {{ $title }}
              </div>

              {{ if $isEpisode }}
                <div class="color-highlight" title="{{ $session.String "title" }}">
                  S{{ $session.Int "parent_media_index" }}&thinsp;·&thinsp;E{{ $session.Int "media_index" }}
                </div>
              {{ else if $isMovie }}
                <div class="color-highlight" title="{{ $session.String "summary" }}">
                  {{ $session.Int "year" }}
                </div>
              {{ end }}

              <div class="color-primary">
                <span title="{{ $session.String "player" }}">
                  {{ $session.String "friendly_name" }}
                </span> •
                <span title="Ends at: {{ $endTime }}">
                  {{ $session.String "progress_percent" }}% watched
                </span>
              </div>
            </div>
          </li>
        {{ end }}
      </ul>
    {{ end }}
```
### Environment Variables
- `TAUTULLI_IP` - local ip address of your tautulli instance
- `TAUTULLI_PORT` - port of your tautulli instance
- `TAUTULLI_API_KEY` - API key for your tautulli instance. Can be found in settings -> Web Interface -> API 
- `SERVER_ID` - Unique ID key for your Plex server. This is required for links to your plex to work. Can be found using this URL {TAUTULLI_IP}:{TAUTULLI_PORT}/api/v2?apikey={TAUTULLI_API_KEY}&cmd=get_server_id&hostname={PLEX_IP}&port={PLEX_PORT} under "identifier"

## Recently Watched
![](recently_watched_small.png)
![](recently_watched_large.png)

### Options
- `size` - Set to "small" for small version, "full" version.
- `collapse` - Set the number of items shown by default in "small" mode before showing 'show more.'

### Environment Variables
- `TAUTULLI_IP` - local ip address of your tautulli instance
- `TAUTULLI_PORT` - port of your tautulli instance
- `TAUTULLI_API_KEY` - API key for your tautulli instance. Can be found in settings -> Web Interface -> API 
- `SERVER_ID` - Unique ID key for your Plex server. This is required for links to your plex to work. Can be found using this URL {TAUTULLI_IP}:{TAUTULLI_PORT}/api/v2?apikey={TAUTULLI_API_KEY}&cmd=get_server_id&hostname={PLEX_IP}&port={PLEX_PORT} under "identifier"