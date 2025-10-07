# CS2 Skin Guessing Game

A daily guessing game built by Ali Abbas Rashid and Sadiq Al-Sandouk where players try to guess a secret CS2 skin selected for the day. Players submit guesses and receive yes/no feedback about the skin's features (for example: is it a CT or T skin? which year was it released? what rarity is it?). The goal is to deduce the target skin with as few guesses as possible.

## Table of Contents

- [Demo](#demo)
- [How it works](#how-it-works)
- [Gameplay & Rules](#gameplay--rules)
- [Data model / API contract](#data-model--api-contract)
- [Scoring](#scoring)
- [Tech / Implementation ideas](#tech--implementation-ideas)
- [Contributing](#contributing)
- [Roadmap & ideas](#roadmap--ideas)

## Demo

This repository holds the game idea and assets. A live demo / playable version will be added later. For now, use the gameplay and API examples below to implement a client or server.

## How it works

- Every day the system selects a single CS2 skin as the "daily skin" (seeded by date).
- Players submit guesses (a skin name or identifier).
- For each guess, the game returns yes/no (or partial) responses about specific features of the skin to help narrow it down.

Example feature questions answered by the game:

- Is the skin for CT or T? (team)
- What year was it released? (year — the game may answer exact year or compare: e.g. ">= 2018")
- What rarity is the skin? (e.g., Mil-Spec, Restricted, Classified, Covert, Exotic)
- Is it from a specific collection? (e.g., Dust 2 Collection)
- Weapon type / slot (rifle, pistol, SMG, shotgun, sniper)
- Is it StatTrak? (yes/no)

The combination of these yes/no answers helps players deduce the correct skin.

## Gameplay & Rules

- One daily skin is chosen and is the same for all players on that day.
- Players make guesses in rounds. After each guess the game returns feedback for a fixed set of feature questions.
- Players keep guessing until they find the skin or reach the maximum number of guesses (configurable).
- Optionally, leaderboards can be kept for fastest correct guesses, fewest guesses, and daily streaks.

Suggested rule set:

- Max guesses per day: 6 (configurable).
- Feedback per guess: a set of boolean or comparative answers for the features listed above.
- Hint system: optionally reveal one feature after N failed guesses.

## Data model / API contract

Here's a suggested canonical format for a skin object and the response for a guess. This helps unify client and server implementations.

Skin object (example):

```json
{
	"id": "ak47_fire_serpent",
	"name": "AK-47 | Fire Serpent",
	"weapon": "rifle",
	"team": "T",             // "T" or "CT" or "both"
	"year": 2014,
	"rarity": "Covert",
	"collection": "Bravo Case",
	"stattrak": false
}
```

Guess request (example):

```json
{ "guess_id": "ak47_wasteland_rebel" }
```

Guess response (example):

```json
{
	"correct": false,
	"feedback": {
		"team": "no",               // yes/no (match)
		"year": "older",           // "exact" / "newer" / "older" / "match"
		"rarity": "no",
		"weapon": "no",
		"collection": "no",
		"stattrak": "no"
	},
	"target_preview": null         // optional safe preview when guessed correctly or after hints
}
```

Notes on feedback semantics:

- For boolean features (team, stattrak) respond with "yes" or "no".
- For ordinal features like year, you can choose to return exact match or relative hints like "newer" / "older" to be more helpful.
- Rarity and weapon type can be exact yes/no matches or categorical hints if you want to broaden gameplay.

## Scoring

- Primary metric: fewest guesses to find the daily skin.
- Secondary metrics: total time taken, streaks (number of consecutive days with a correct guess), and daily leaderboard position.

Optional scoring example:

- Correct on guess 1: 100 points
- Correct on guess 2: 70 points
- Correct on guess 3: 50 points
- Each incorrect guess: -5 points

## Tech / Implementation ideas

This game can be implemented as a simple web app, CLI, or mobile app. Suggestions:

- Backend: Node.js + Express, or Python + FastAPI. Keep an in-memory or small database (SQLite / JSON) of skins.
- Frontend: React / Vue for a web client, or plain HTML/CSS/JS for a lightweight version.
- Persistence: small JSON or SQLite store for the skin catalog + user stats. Use date-based seeding to select the daily skin.
- Deployment: static frontend + serverless functions (Vercel, Netlify) or a simple Heroku/Azure Web App.

Minimal server responsibilities:

1. Host the skin catalog and pick a daily target (seeded deterministic by date).
2. Accept guesses and return structured feedback.
3. Store user stats, optionally leaderboard entries.

Deterministic daily selection (example in pseudocode):

```
seed = hash(YYYY-MM-DD)
index = seed % skins.length
target = skins[index]
```

## Contribution

This project is created by Ali Abbas Rashid and Sadiq Al-Sandouk. Contributions are welcome!

How to contribute:

1. Fork the repo and create a topic branch.
2. Add or fix features, add unit tests where applicable.
3. Open a pull request with a clear description of changes.

Guidelines:

- Keep the skin data canonical (id, name, weapon, team, year, rarity, collection, stattrak).
- Add tests for the daily-seed selection logic and guess-feedback logic.

## Roadmap & ideas

- Add a polished web UI with daily share cards (like Wordle).
- Add a multiplayer mode where players compete in real time.
- Add difficulty modes (narrow feedback vs. broad feedback).
- Add more complex hints (color palette, finish type, pattern index range).

## Edge cases to consider

- Skins that are available to both teams. Decide how "team" is handled (both/neutral).
- Multiple skins with the same name across weapons — use unique `id`.
- Internationalization of skin names.
- Handling newly-released skins: how often the catalog updates and whether a daily seed picks a new skin immediately.

---

If you'd like, I can also:

- scaffold a minimal Node + Express server and a simple React frontend to make this playable locally, or
- add a starter skin catalog JSON and a deterministic daily picker implementation.

Tell me which of those you'd like next and I'll implement it and run quick validation.

Contributors: Ali Abbas Rashid and Sadiq Al-Sandouk
