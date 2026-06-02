# Marriage Score Calculator

A web-based scorecard for the card game Marriage. Tracks Maal, Hand, and scores across rounds for 2-6 players, with automatic scoring, color-coded rankings, and a built-in rules guide.

**Live:** https://kanodiasarthak.github.io/marriage-scorecard/

---

## Origin

Growing up in a joint family, card games were always part of how we spent time together. Over the past few years, Marriage has been the go-to game, and for a long time scores were tracked with pen and paper.

A couple of years ago I built a Google Sheet to handle the scoring, mainly to save paper and cut down on calculation errors. It got the job done, but every session required duplicating the original tab, and using it on mobile was not a great experience. I wanted something that felt like a proper tool rather than a workaround, while staying simple enough that anyone could pick it up mid-game.

The sheet was exported to Excel and given to Claude to convert into a standalone HTML app. Claude got the scoring formulas right from the sheet structure but needed context on the game logic, things like what Maal, Hand, and Show mean, how the scoring formula works, and what the UI should feel like. After about 1.5 hours of back and forth, v1 was ready, manually tested, and shared with end users.

The [original Google Sheet](https://docs.google.com/spreadsheets/d/1KR6iHZWqNtxvxeLNS6Ndy2l8QXOOouTa-C4rMYUYozw/edit?usp=sharing) has since been archived but is viewable. The 3rd February tab shows how an actual scorecard looked. The HTML tool follows the same basic structure and carries over several UI elements, but improves on a few things the sheet could not do:

| | Google Sheet | HTML Tool |
|---|---|---|
| Round score visibility | Columns hidden to keep the sheet navigable | Merged rows, always visible |
| Rules reference | None | Built into the footer |
| Session setup | Duplicate the tab each time | Open and play |
| Mobile experience | Functional but not great | Built for it |
| Table size | Fixed layout regardless of player count or rounds played | Renders only the columns and rows needed for the current game |
| Player addition mid-game | Not supported | Supported from round 2 onward |
| Hand limit variant | Not supported | Standard (10) or Variant (15) |

---

## Features

- 2-6 players with editable names
- Maal and Hand tracked per player per round
- Automatic scoring with color-coded rankings (green to red)
- Show player highlighted per round
- Running totals and leaderboard positions
- Hand limit variant: Standard (10) or Variant (15)
- Mid-game player addition with join-round tracking
- Built-in rules guide in the footer

---

## Usage

No installation required. Open `index.html` in any browser or use the [live version](https://kanodiasarthak.github.io/marriage-scorecard/).

1. Select the **Hand limit**: Standard (10) or Variant (15)
2. Select the **number of players**
3. Enter player names
4. For each round, select who showed and enter Maal and Hand for each player
5. Click **+ Add Round** to proceed. The current round must be complete first
6. To add a player mid-game, click **+ Add Player** (available from round 2 onward)
7. To start over, click **Reset Game**. This clears all rounds, names, and unlocks the hand limit and player count

---

## Iteration

After v1 was tested by end users, two requests came back:

- A hand limit variant where the max Hand is 15 instead of 10
- The ability to add a player mid-game

Both were done in a second session by giving Claude the existing `index.html` and describing the changes. That took about 30 minutes.

---

## Lock Logic and Design Decisions

**Hand limit locks when you select the number of players.** The hand limit is a game-level setting that should be agreed on before play starts. Allowing it to change after rounds are entered would affect how existing hands are validated, which would cause confusion. Selecting the number of players is the natural point where setup ends.

**Player count locks once round 2 is added.** Round 1 is treated as still being in setup. If you picked the wrong number of players, you can still correct it before the game is properly underway. Once a second round exists, changing the player count would break existing round data.

**A new player can only be added from round 2 onward.** There needs to be at least one completed round to account for before a new player joins. The button does not appear until then.

**Late joiners have their past rounds set to zero and locked.** When a player joins mid-game, their Maal and Hand for all previous rounds are set to 0 and cannot be changed. The scoring formula depends on a consistent set of active players per round, so past rounds stay as-is. Those score cells show a dash to make it clear the player was not part of them. The player also gets a "from RN" badge in the players table showing which round they joined.

**The show player's Hand is locked to 0.** The player who shows has no unmatched cards, so the field is locked to avoid a potential input error.

**You cannot add a new round until the current one is complete.** This stops partial data from affecting the running totals and leaderboard. The app flags the missing fields and lists what needs to be filled in before you can proceed.

---

## Tests

After the second round of changes, Claude wrote a test file (`tests.html`) covering the core scoring logic and ran it. 4 out of 54 tests failed. The developer flagged this and Claude found that all 4 failures were wrong expectations in the tests themselves, not bugs in the app. The assertions were corrected and the suite was done.

Open `tests.html` in any browser to run the suite. It covers:

- Scoring formula correctness across 2-6 players
- Multi-round total accumulation
- Zero-sum invariant at all player counts
- Hand limit enforcement (standard and variant)
- Round completeness validation
- Mid-game player addition and lock logic
- Edge cases: zero maal, maximum values, late joiners

> The test file has not been reviewed line by line by a human. It reflects what Claude chose to test and how that compares to the manual testing already done.

---

## Stack

Single-file HTML, CSS, and vanilla JavaScript. No frameworks, no build step. Uses Google Fonts for typography and falls back to system fonts if unavailable.
