# callbacks

Static OAuth redirect pages for [sonn](https://github.com/sonn-audio/core), served from
GitHub Pages at `https://sonn-audio.github.io/callbacks/`.

A sonn server usually lives on a private network, so it has no public HTTPS URL of its own.
Streaming services will only redirect an OAuth flow to a fixed, pre-registered address. These
pages are that fixed address: they take the authorization code the service hands them and bounce
it on to whatever local server started the flow.

Nothing here talks to a sonn server directly, and nothing is stored — the pages only redirect.

## Layout

| Path | Purpose |
| --- | --- |
| `spotify/index.html` | Spotify OAuth redirect target |
| `404.html` | Handles the path-encoded form of the same redirect |

The server passes its own callback URL in the `state` parameter, percent-encoded. The page reads
`state` plus `code` from the query string and redirects to `<state>?code=<code>`.

`404.html` covers an older variant where the local URL was appended to the path
(`/callbacks/spotify/<encoded-url>`) instead of passed as `state`. GitHub Pages serves it for any
unmatched path, and it rewrites the request into the query form above.

## Why this is a separate repository

The URL is registered in the Spotify developer dashboard as an allowed redirect URI, matched
exactly. If it moves, every existing install loses the ability to link an account until the
dashboard and the server are both updated. Keeping these pages out of the server repository means
they do not move when the server is restructured or released.

For the same reason `spotify/` is a directory rather than a single file: the trailing-slash URL
stays valid regardless of how the page itself is later rewritten.

## Changing a path

1. Update `callbackBase` in both `spotify/index.html` and `404.html`.
2. Add the new URL to the allowed redirect URIs in the Spotify developer dashboard.
3. Update `SPOTIFY_PUBLIC_REDIRECT_URI` in `sonn-audio/core`
   (`src/adapters/content/providers/spotify/serviceAuth.ts`).

Leave the old URL registered and the old page in place until installs have had time to update.
