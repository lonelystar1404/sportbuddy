# SportBuddy

Find people to play sports with nearby. No sign-up: people can add a display name, which is kept in their browser.

## Run it on your computer (localhost)
1. Install Node.js 18 or newer from https://nodejs.org (skip if `node -v` already works).
2. Download `index.html`, `server.js` and `package.json` into one folder, e.g. `sportbuddy`.
3. Open a terminal in that folder and run:
   ```
   npm start
   ```
4. Open http://localhost:4747 in your browser and allow location when it asks.

Use another port with `PORT=8080 npm start` (Windows PowerShell: `$env:PORT=8080; npm start`). Stop it with Ctrl+C.
No Node? From the same folder, `python3 -m http.server 4747` works too.

## What it does
- **Find a game**: calendar of upcoming games, filter by sport, distance and open spots.
- **Host a game**: sport, date, time, length, players needed, backup list size, place, level, cost, notes.
- **Public or private games**: public games are listed and the host accepts or declines each join request. Private games are hidden and open only through the host's invite link (`#invite=<gameId>.<code>`).
- **Join / backup list**: when a game is full, people join the backup list and move up automatically if a player leaves. Hosts can remove players or cancel the game.
- **Places to play**: searches OpenStreetMap around your location for courts and fields for the chosen sport. Prices show only where the map lists them (`fee` / `charge` tags), otherwise "Price not listed". Pickleball falls back to tennis courts when few pickleball courts are mapped.
- **Location**: asks for browser location, or you can type a city or ZIP.

## Files
- `index.html`: the full site as one standalone file. Open it in a browser or host it on any static host (GitHub Pages, Netlify, Vercel). Location and place search work here.
- `server.js` + `package.json`: a tiny local server with no dependencies (`npm start`).
- `artifact.html`: the same page in the form published as the Claude preview.

## Where games are stored
- **In the Claude preview**: games are in a shared database attached to the page, so everyone the page is shared with (as Contributor) sees the same games.
- **Website (GitHub Pages or localhost)**: games and join requests are stored in Firebase Firestore (project `sportbuddy-ec851`), so everyone who opens the site sees the same games, with live updates. If Firebase can't be reached, the page falls back to saving games in the visitor's browser (localStorage). The Firestore rules only allow reading and writing the `games` and `signups` collections.

## Data model
- `games/{id}`: sport, date, time, durationMin, maxPlayers, maxBackups, venueName, lat, lon, area, level, cost, notes, hostKey, hostName, createdAt, visibility (`public` | `private`), approval (public games: host accepts each player), inviteCode (private games)
- `signups/{gameId_userKey}`: gameId, key, name, joinedAt. status: `pending` (join request waiting for the host) or `declined`; no status means accepted. The first `maxPlayers` accepted signups by acceptance time are players; the rest are the backup list.

## Community tab

Members sign up with email and password (Firebase Authentication) to read and share posts with text, up to 4 photos, and an optional sport tag, then like (or double-tap a photo), comment, and filter the feed by sport or person. Photos are resized in the browser and stored inside the post document, so no paid Firebase Storage plan is needed. Posts live in the Firestore `posts` collection and comments in `posts/{id}/comments`; only signed-in members can read them.

## Light and dark mode

The header button switches between Auto (follows the device), Light, and Dark. The choice is remembered in the browser.
