# Gradient 🌈

A wavelength-style party game: one psychic, one hidden target on a semicircular dial, and friends trying to read their mind. Multiplayer rooms, custom prompts, prompt packs, freeform mode, and local high-score history.

Static frontend (host anywhere, e.g. GitHub Pages) + Firebase Realtime Database for room sync. Both are free.

## Setup (~10 minutes, one time)

### 1. Create a Firebase project
1. Go to https://console.firebase.google.com and sign in with a Google account.
2. **Add project** → name it (e.g. `gradient-game`) → you can disable Google Analytics → **Create**.

### 2. Create a Realtime Database
1. In the left sidebar: **Build → Realtime Database → Create Database**.
2. Pick a location (e.g. `europe-west1` if you're in NL) → start in **locked mode** → **Enable**.
3. Go to the **Rules** tab and replace the rules with:

```json
{
  "rules": {
    "rooms": {
      "$code": {
        ".read": true,
        ".write": true,
        ".validate": "newData.hasChildren() || newData.val() == null"
      }
    }
  }
}
```

Click **Publish**. (These rules are open by design — anyone who knows your database URL could write to `rooms/`. That's fine for a casual friends game; see "Honesty notes" below.)

### 3. Get your config and paste it into `index.html`
1. Project overview → gear icon → **Project settings** → scroll to **Your apps** → click the **`</>`** (web) icon.
2. Register the app (any nickname, no hosting needed) → Firebase shows a `firebaseConfig` object.
3. Open `index.html`, find the `firebaseConfig` block near the top of the `<script>`, and replace the `PASTE_YOURS` values with yours. Make sure `databaseURL` is included — if the snippet doesn't show it, copy the URL from the Realtime Database page (looks like `https://YOUR-PROJECT-default-rtdb.europe-west1.firebasedatabase.app`).

### 4. Host on GitHub Pages
1. Create a new **public** repository on GitHub (e.g. `gradient`).
2. Upload `index.html` (and this README) to the repo root — the web UI's "Add file → Upload files" works fine.
3. Repo **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main`, folder `/ (root)` → **Save**.
4. After a minute your game is live at `https://YOURUSERNAME.github.io/gradient/`.

### 5. Play
1. Open the site, enter your name, **Create a room**.
2. Send friends the invite link (or show the QR code) — the room code is in the URL, so they just enter a name and they're in.
3. Host picks rounds, difficulty, and prompt packs, then starts.

## How a round works
- The **psychic** (rotates each round) privately sees the target on the dial and types or says a clue.
- Everyone else drags their own needle and locks in a guess.
- The team scores on the **average** of all guesses: 4 / 3 / 2 / 0 points depending on distance. Everyone's individual needle is shown on the reveal, so you know exactly who dragged the average off a bullseye.

## Features
- **Prompt packs**: Classic, Food & Drink, Pop Culture, People & Habits, Big Ideas — toggle per game.
- **Custom prompts**: add your own spectrum pairs (stored in your browser); they're shuffled into any game you host.
- **Freeform mode**: the psychic types a brand-new spectrum every round.
- **Difficulty**: shrinks the target wedge.
- **High scores**: finished games are saved locally on each player's device with names, so you remember who you're telepathic with.

## Honesty notes / limitations
- The target angle lives in the shared room state, so a determined cheater could read it from browser dev tools. Same trade-off longwave-style games make — play with people you like.
- Database rules are open. If a stranger found your DB URL they could scribble in `rooms/`. No personal data is stored, and the free tier caps abuse, but don't reuse this Firebase project for anything else.
- Old rooms aren't auto-deleted. Occasionally clear the `rooms` node in the Firebase console (Realtime Database → Data → hover `rooms` → ✕), or leave it — a few stale rooms cost nothing.
- If the **host** closes their tab mid-game, the room is deleted when they press "Leave room", but if they just disconnect, the game stalls at the next host-only step (reveal advance / next round). Refresh-and-rejoin with the same browser restores their host powers.
