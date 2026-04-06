---
name: learn-french
description: Personal French language learning system built on Tim Ferriss's DiSSS/CaFE method. Sets up the Deconstruction Dozen session, daily 8am vocab drills with spaced repetition, on-demand conversation practice, and a progress tracker. Triggers on "learn french", "french setup", "start french", "/french-start", "/fr-start".
---

# Learn French — Ferriss DiSSS Method

Sets up a complete French learning system inside GhostClaw using Tim Ferriss's DiSSS/CaFE methodology: one-time grammar deconstruction, frequency-ordered spaced repetition, and AI conversation practice pitched at your level.

## Overview of what this skill creates

| File | Purpose |
|------|---------|
| `groups/main/french/grammar-profile.md` | Personal grammar cheat sheet generated from Deconstruction Dozen |
| `groups/main/french/vocab-progress.json` | Spaced repetition state (word, status, next_review, interval_days) |
| `groups/main/french/session-state.json` | Active conversation session (if any) |
| `groups/main/french/stats.json` | Streak, days active, session count |

## Setup

### 1. Create the french/ directory and seed files

```bash
mkdir -p $GHOSTCLAW_GROUP_DIR/french
```

Create `$GHOSTCLAW_GROUP_DIR/french/stats.json` if it doesn't exist:

```json
{
  "days_active": 0,
  "streak": 0,
  "last_active": null,
  "sessions_completed": 0,
  "deconstruction_done": false
}
```

Create `$GHOSTCLAW_GROUP_DIR/french/vocab-progress.json` if it doesn't exist, seeded with the top 200 French frequency words (see **Starter Word List** section below). Each entry:

```json
[
  {
    "rank": 1,
    "word": "le",
    "translation": "the (masc.)",
    "status": "new",
    "next_review": null,
    "interval_days": 1,
    "correct_streak": 0
  }
]
```

### 2. Add French commands to CLAUDE.md

Append the following block to `$GHOSTCLAW_GROUP_DIR/CLAUDE.md`:

---

```markdown
## French Learning System

Commands: `/fr-start` (or `/french-start`), `/fr`, `/fr-stop`, `/fr-stats`

All French data lives in `french/` (relative to `$GHOSTCLAW_GROUP_DIR`).

### `/fr-start` — Deconstruction Dozen (run once)

Check `french/stats.json`. If `deconstruction_done` is true, tell the user it's already been completed and offer to redo it.

Walk through the 12 Ferriss Deconstruction sentences one at a time via Telegram. For each sentence:
1. Send the English sentence and ask the user to either look up the French translation themselves, or type "translate" to have you provide it.
2. Once you have the French translation, analyse what grammatical patterns it reveals (gender marking, verb conjugation pattern, pronoun placement, subject–verb agreement, etc.).
3. Send a short analysis (2–4 lines) of the patterns revealed.

The 12 sentences:
1. "The apple is red." — reveals: gendered noun, copula, adjective agreement
2. "It is John's apple." — reveals: possessive construction (de)
3. "I give John the apple." — reveals: indirect object placement
4. "We give him the apple." — reveals: indirect object pronoun (lui)
5. "He gives it to her." — reveals: direct + indirect pronouns stacked (le/la + lui)
6. "She gives it to us." — reveals: first-person plural pronoun (nous)
7. "I must give it to him." — reveals: modal verb + infinitive structure
8. "I want to give it to her." — reveals: vouloir + infinitive
9. "I'm going to know tomorrow." — reveals: futur proche (aller + infinitive)
10. "I have eaten the apple." — reveals: passé composé (avoir + participe passé)
11. "I can't eat the apple." — reveals: negation (ne…pas) + pouvoir + infinitive
12. "I eat the apple every day." — reveals: présent simple for habitual actions

After all 12 are done:
- Extract and summarise the key grammar rules revealed (SVO order, noun gender, adjective agreement after noun, pronoun stacking order, passé composé vs. imparfait hint, negation pattern, modal + infinitive, futur proche).
- Write `french/grammar-profile.md` with a clean one-page cheat sheet (see template below).
- Set `deconstruction_done: true` in `french/stats.json`.
- Send a message: "Deconstruction done. Grammar profile saved. Daily drills will start at 8am tomorrow."

**grammar-profile.md template:**
```markdown
# French Grammar Profile
*Generated: [date]*

## Sentence Structure
SVO (Subject–Verb–Object), same as English.

## Noun Gender
Every noun is masculine (le/un) or feminine (la/une). Must be memorised with the word.
Key tip: [any patterns noticed during deconstruction]

## Adjective Agreement
Adjectives come AFTER the noun and agree in gender/number.
e.g. une pomme rouge (f.), un livre rouge (m.)

## Pronoun Order
Direct before indirect: le/la/les → lui/leur
Both before the verb: Je le lui donne.

## Key Conjugations Revealed
| Infinitive | je | nous | notes |
|-----------|-----|------|-------|
| être (to be) | suis | sommes | |
| avoir (to have) | ai | avons | |
| donner (to give) | donne | donnons | regular -er |
| pouvoir (can) | peux | pouvons | irregular |
| vouloir (want) | veux | voulons | irregular |
| aller (to go) | vais | allons | futur proche: aller + inf |

## Tenses
- **Présent**: habitual/current actions
- **Passé composé**: completed past (avoir/être + past participle)
- **Futur proche**: near future (aller + infinitive)

## Negation
ne…pas wraps the conjugated verb: Je ne mange pas.
With modals: Je ne peux pas manger.

## Questions
Rising intonation (spoken), Est-ce que… (neutral), inversion (formal).
```

---

### `/fr` — Conversation Practice

Load context:
1. Read `french/grammar-profile.md` (the user's personal cheat sheet)
2. Read `french/vocab-progress.json` — extract all words with status `learning` or `known`
3. Read `french/session-state.json` if it exists

Set session state in `french/session-state.json`:
```json
{
  "active": true,
  "started_at": "<ISO timestamp>",
  "last_message_at": "<ISO timestamp>",
  "words_encountered": [],
  "errors_made": []
}
```

**Conversation rules:**
- Respond **entirely in French** (no English translation unless asked)
- Pitch language just above known vocab — Krashen i+1: use mostly known words, introduce 1–2 new words per exchange
- When the user makes a grammar or vocab error, correct it inline in brackets: e.g. "*(tu veux, pas tu veut)* — Oui, je veux…"
- Do NOT explain the correction at length — just model the correct form and continue
- If the user seems stuck, offer a one-line hint: "*(Hint: use passé composé here)*"
- Keep messages short — Telegram-friendly, 2–5 sentences

**Starting the session:**
Open with a simple, friendly French opener at their level. If grammar-profile.md exists, assume beginner/A1-A2. If they have 100+ known words, pitch at A2-B1. Example opener: "Bonjour ! Comment ça va aujourd'hui ? Tu as fait quelque chose d'intéressant ?"

Update `session-state.json` after each exchange (last_message_at, words_encountered, errors_made).

---

### `/fr-stop` — End Conversation Session

Read `french/session-state.json`. If no active session, say so.

Otherwise:
1. Set `active: false` in session-state.json
2. Count words_encountered that aren't already `known` in vocab-progress.json
3. For each new word encountered: add to vocab-progress.json with status `learning`, interval_days 1, next_review = tomorrow
4. Update stats.json: increment sessions_completed, update last_active, recalculate streak
5. Send a brief session summary:
   - Duration
   - New words encountered: [list]
   - Errors to review: [list if any]

---

### `/fr-stats` — Progress Report

Read `french/stats.json` and `french/vocab-progress.json`.

Calculate:
- `known_count` = words with status `known`
- `learning_count` = words with status `learning`
- `new_count` = words with status `new` (not yet seen)
- `due_count` = words where `next_review <= today` and status is `learning`

Send a Telegram message:

```
*French Progress*

📅 Days active: [N]
🔥 Streak: [N] days
📚 Sessions: [N]

*Vocabulary*
✅ Known: [N] / 1,200
🔄 Learning: [N]
📬 Due for review: [N]
🆕 Not yet seen: [N]

*Deconstruction*: [Done ✓ / Not started]

Progress to conversational fluency: [N]%
```

Progress % = (known_count / 1200) * 100, capped at 100.

---

### Daily Vocab Drill (cron, 8am)

This runs automatically at 8am every day.

**Algorithm:**
1. Read `french/vocab-progress.json`
2. Collect **due words**: status `learning` AND `next_review <= today`. Take up to 20.
3. Collect **new words**: status `new`, sorted by rank (lowest rank first). Take enough to reach 10 total (10 - due_count, minimum 0).
4. If 0 due + 0 new: send "No drill today — you're all caught up! Use /fr to practice conversation."
5. Otherwise, send a drill via Telegram.

**Drill format (send as one message):**

```
*French Drill — [date]*

Tap the word to reveal the translation. Reply with 1️⃣ for each one you knew instantly.

1. *le* — ?
2. *être* — ?
3. *grand* — ?
...
```

Then send a follow-up: "Reply with the numbers you got right (e.g. `1 3 5`) or `all` / `none`."

**After user replies:**
- For each word marked correct: double interval_days (cap at 30), set next_review = today + interval_days, update correct_streak; if correct_streak >= 3, set status `known`
- For each word marked wrong: set interval_days = 1, next_review = tomorrow, set correct_streak = 0
- Save updated vocab-progress.json
- Update stats.json: update last_active, recalculate streak
- Send confirmation: "Drill done. [N] known, [N] review tomorrow."

**Streak calculation:**
- If last_active was yesterday or today: streak += 1
- If last_active was before yesterday: streak = 1
- Update last_active to today
```

---

### 3. Register the daily vocab drill cron

Get the main chat JID:

```bash
sqlite3 $GHOSTCLAW_GLOBAL_DIR/store/messages.db "SELECT jid FROM registered_groups WHERE folder = 'main' LIMIT 1;"
```

Write the scheduled task:

```bash
CHAT_JID="<jid from above>"
cat > $GHOSTCLAW_IPC_DIR/tasks/french_vocab_drill_$(date +%s).json << EOF
{
  "type": "schedule_task",
  "prompt": "Run the daily French vocab drill. Read $GHOSTCLAW_GROUP_DIR/french/vocab-progress.json. Collect due words (status 'learning' and next_review <= today's date). Collect new words (status 'new', sorted by rank ascending) to reach a total of 10 words. Send the drill to the user via send_message. Wait for their reply (numbers they got right, or 'all'/'none'). Update vocab-progress.json with spaced repetition results (correct = double interval, cap 30 days; wrong = reset to 1 day). If correct_streak >= 3 set status to 'known'. Update $GHOSTCLAW_GROUP_DIR/french/stats.json: last_active today, recalculate streak (yesterday or today = +1, older = reset to 1). Send a short confirmation when done.",
  "schedule_type": "cron",
  "schedule_value": "0 8 * * *",
  "context_mode": "isolated",
  "targetJid": "$CHAT_JID"
}
EOF
```

### 4. Seed vocab-progress.json with the starter word list

Write `$GHOSTCLAW_GROUP_DIR/french/vocab-progress.json` with the following data. This is the top 200 most frequent French words by corpus frequency. The full 1,200-word list can be found at: https://en.wiktionary.org/wiki/Wiktionary:Frequency_lists/French_wordlist — add those words in rank order (201–1200) with status `new` to extend the list.

```json
[
  {"rank":1,"word":"le","translation":"the (masc. sing.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":2,"word":"de","translation":"of / from","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":3,"word":"un","translation":"a / one (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":4,"word":"être","translation":"to be","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":5,"word":"et","translation":"and","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":6,"word":"à","translation":"at / to / in","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":7,"word":"il","translation":"he / it (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":8,"word":"avoir","translation":"to have","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":9,"word":"ne","translation":"not (negative particle)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":10,"word":"je","translation":"I","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":11,"word":"son","translation":"his / her / its (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":12,"word":"que","translation":"that / what / which","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":13,"word":"se","translation":"oneself (reflexive)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":14,"word":"qui","translation":"who / which","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":15,"word":"ce","translation":"this / that / it","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":16,"word":"en","translation":"in / of it / some","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":17,"word":"du","translation":"of the (masc.) / some","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":18,"word":"elle","translation":"she / it (fem.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":19,"word":"au","translation":"at the / to the (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":20,"word":"des","translation":"of the (pl.) / some","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":21,"word":"les","translation":"the (pl.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":22,"word":"pas","translation":"not (with ne)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":23,"word":"on","translation":"one / we (informal)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":24,"word":"plus","translation":"more / no more","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":25,"word":"par","translation":"by / through / per","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":26,"word":"mais","translation":"but","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":27,"word":"vous","translation":"you (formal / pl.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":28,"word":"nous","translation":"we / us","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":29,"word":"lui","translation":"him / to him/her","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":30,"word":"si","translation":"if / yes (contradiction)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":31,"word":"tout","translation":"all / everything","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":32,"word":"bien","translation":"well / good","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":33,"word":"faire","translation":"to do / to make","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":34,"word":"sur","translation":"on / about / over","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":35,"word":"leur","translation":"their / to them","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":36,"word":"grand","translation":"big / great / tall","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":37,"word":"avec","translation":"with","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":38,"word":"non","translation":"no / not","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":39,"word":"pouvoir","translation":"to be able to / can","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":40,"word":"même","translation":"same / even / itself","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":41,"word":"aussi","translation":"also / too","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":42,"word":"dans","translation":"in / inside","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":43,"word":"ou","translation":"or","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":44,"word":"y","translation":"there / to it","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":45,"word":"comme","translation":"as / like / how","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":46,"word":"dont","translation":"of which / whose","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":47,"word":"autre","translation":"other / another","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":48,"word":"peu","translation":"little / few","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":49,"word":"homme","translation":"man / human","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":50,"word":"vouloir","translation":"to want","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":51,"word":"dire","translation":"to say / to tell","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":52,"word":"temps","translation":"time / weather","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":53,"word":"très","translation":"very","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":54,"word":"savoir","translation":"to know (facts/how)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":55,"word":"falloir","translation":"to be necessary (il faut)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":56,"word":"voir","translation":"to see","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":57,"word":"chose","translation":"thing","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":58,"word":"quand","translation":"when","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":59,"word":"aller","translation":"to go","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":60,"word":"encore","translation":"still / again / even","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":61,"word":"la","translation":"the (fem. sing.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":62,"word":"où","translation":"where","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":63,"word":"oui","translation":"yes","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":64,"word":"jour","translation":"day","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":65,"word":"deux","translation":"two","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":66,"word":"bon","translation":"good (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":67,"word":"nouveau","translation":"new (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":68,"word":"premier","translation":"first (masc.)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":69,"word":"venir","translation":"to come","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":70,"word":"prendre","translation":"to take","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":71,"word":"vie","translation":"life","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":72,"word":"après","translation":"after / afterwards","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":73,"word":"sous","translation":"under / below","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":74,"word":"rien","translation":"nothing","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":75,"word":"depuis","translation":"since / for (time)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":76,"word":"donner","translation":"to give","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":77,"word":"toujours","translation":"always / still","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":78,"word":"an","translation":"year","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":79,"word":"avant","translation":"before / in front","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":80,"word":"alors","translation":"then / so","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":81,"word":"car","translation":"because / for (written)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":82,"word":"là","translation":"there / here","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":83,"word":"sans","translation":"without","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":84,"word":"monde","translation":"world / people","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":85,"word":"contre","translation":"against","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":86,"word":"deja","translation":"already","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":87,"word":"moi","translation":"me / I (emphatic)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":88,"word":"toi","translation":"you (emphatic / informal)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":89,"word":"heure","translation":"hour / time (o'clock)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":90,"word":"passer","translation":"to pass / to spend (time)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":91,"word":"France","translation":"France","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":92,"word":"entre","translation":"between / among","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":93,"word":"mettre","translation":"to put / to place","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":94,"word":"main","translation":"hand","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":95,"word":"part","translation":"part / share / aside","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":96,"word":"enfant","translation":"child","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":97,"word":"rendre","translation":"to give back / to render","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":98,"word":"demander","translation":"to ask / to request","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":99,"word":"seul","translation":"alone / only / single","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":100,"word":"leur","translation":"their (pl. possessive)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":101,"word":"pays","translation":"country","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":102,"word":"parler","translation":"to speak / to talk","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":103,"word":"trouver","translation":"to find","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":104,"word":"femme","translation":"woman / wife","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":105,"word":"politique","translation":"politics / political","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":106,"word":"certain","translation":"certain / sure / some","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":107,"word":"devoir","translation":"to have to / must / duty","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":108,"word":"point","translation":"point / dot / not at all","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":109,"word":"fois","translation":"time (occasion)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":110,"word":"eau","translation":"water","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":111,"word":"cas","translation":"case / situation","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":112,"word":"peut-être","translation":"maybe / perhaps","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":113,"word":"monsieur","translation":"Mister / sir / gentleman","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":114,"word":"comprendre","translation":"to understand","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":115,"word":"pendant","translation":"during / for (time span)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":116,"word":"place","translation":"place / seat / square","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":117,"word":"dernier","translation":"last / latest","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":118,"word":"ami","translation":"friend","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":119,"word":"état","translation":"state / condition","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":120,"word":"travail","translation":"work / job","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":121,"word":"connaître","translation":"to know (person/place)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":122,"word":"suivre","translation":"to follow","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":123,"word":"penser","translation":"to think","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":124,"word":"trop","translation":"too much / too many","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":125,"word":"gouvernement","translation":"government","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":126,"word":"sembler","translation":"to seem / to appear","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":127,"word":"tenir","translation":"to hold / to keep","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":128,"word":"ville","translation":"city / town","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":129,"word":"travail","translation":"work / job / labour","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":130,"word":"nuit","translation":"night","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":131,"word":"mère","translation":"mother","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":132,"word":"père","translation":"father","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":133,"word":"eau","translation":"water","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":134,"word":"mort","translation":"death / dead","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":135,"word":"question","translation":"question","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":136,"word":"donc","translation":"so / therefore / thus","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":137,"word":"corps","translation":"body","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":138,"word":"appeler","translation":"to call","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":139,"word":"valeur","translation":"value / worth","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":140,"word":"milieu","translation":"middle / environment","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":141,"word":"manger","translation":"to eat","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":142,"word":"force","translation":"strength / force","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":143,"word":"entendre","translation":"to hear / to understand","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":144,"word":"rester","translation":"to stay / to remain","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":145,"word":"laisser","translation":"to leave / to let","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":146,"word":"moment","translation":"moment / time","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":147,"word":"beaucoup","translation":"a lot / many / much","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":148,"word":"tête","translation":"head","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":149,"word":"partir","translation":"to leave / to go away","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":150,"word":"parce que","translation":"because","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":151,"word":"ligne","translation":"line","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":152,"word":"ouvrir","translation":"to open","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":153,"word":"semaine","translation":"week","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":154,"word":"soir","translation":"evening","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":155,"word":"matin","translation":"morning","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":156,"word":"livre","translation":"book / pound (currency)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":157,"word":"nombre","translation":"number","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":158,"word":"face","translation":"face / opposite","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":159,"word":"marcher","translation":"to walk / to work (function)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":160,"word":"social","translation":"social","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":161,"word":"position","translation":"position","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":162,"word":"regarder","translation":"to look at / to watch","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":163,"word":"produire","translation":"to produce","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":164,"word":"moyen","translation":"means / average / medium","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":165,"word":"retourner","translation":"to return / to go back","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":166,"word":"porte","translation":"door / gate","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":167,"word":"droit","translation":"right / straight / law","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":168,"word":"guerre","translation":"war","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":169,"word":"bout","translation":"end / tip / bit","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":170,"word":"depuis","translation":"since / from (time)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":171,"word":"arriver","translation":"to arrive / to happen","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":172,"word":"âge","translation":"age","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":173,"word":"accord","translation":"agreement / chord","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":174,"word":"sortir","translation":"to go out / to exit","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":175,"word":"répondre","translation":"to answer / to reply","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":176,"word":"jamais","translation":"never / ever","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":177,"word":"haut","translation":"high / tall / top","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":178,"word":"long","translation":"long","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":179,"word":"vrai","translation":"true / real","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":180,"word":"propre","translation":"own / clean","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":181,"word":"pied","translation":"foot","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":182,"word":"ordre","translation":"order / command","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":183,"word":"côté","translation":"side / next to","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":184,"word":"nom","translation":"name / noun","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":185,"word":"ainsi","translation":"thus / so / in this way","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":186,"word":"form","translation":"form / shape","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":187,"word":"exemple","translation":"example","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":188,"word":"mois","translation":"month","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":189,"word":"lettre","translation":"letter","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":190,"word":"mourir","translation":"to die","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":191,"word":"finir","translation":"to finish / to end","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":192,"word":"courir","translation":"to run","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":193,"word":"chaque","translation":"each / every","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":194,"word":"travers","translation":"across / through (à travers)","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":195,"word":"parmi","translation":"among","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":196,"word":"tellement","translation":"so much / so many","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":197,"word":"sentir","translation":"to feel / to smell","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":198,"word":"devenir","translation":"to become","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":199,"word":"croire","translation":"to believe / to think","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":200,"word":"permettre","translation":"to allow / to permit","status":"new","next_review":null,"interval_days":1,"correct_streak":0}
]
```

**To extend to 1,200 words:**
Get the full Wiktionary French frequency list at https://en.wiktionary.org/wiki/Wiktionary:Frequency_lists/French_wordlist and append words ranked 201–1200 in the same JSON format (status: "new", next_review: null, interval_days: 1, correct_streak: 0).

### 5. Confirm setup

Tell the user:

> "French learning system is ready.
>
> - `/fr-start` — run the Deconstruction Dozen (do this first, ~20 min)
> - `/fr` — start a conversation session in French
> - `/fr-stop` — end the session and save new vocabulary
> - `/fr-stats` — see your progress
>
> Daily 10-word vocab drills start at 8am. The first drill will arrive tomorrow morning."

## Method Reference

This skill implements Tim Ferriss's DiSSS/CaFE framework:

| Principle | Implementation |
|-----------|---------------|
| **Deconstruction** | Deconstruction Dozen (12 sentences unlock grammar system) |
| **Selection (80/20)** | Top 1,200 frequency words = conversational fluency threshold |
| **Sequencing** | Grammar first (day 1) → core 100 → build to 1,200 |
| **Compression** | grammar-profile.md = one-page personal cheat sheet |
| **Frequency** | Daily 10-word drills + conversation sessions |
| **Encoding** | Krashen i+1 in conversation (new words in context, not isolation) |

Target: **B1–B2 level** (conversational competency) at ~1,200 known words.

## Session auto-timeout

The `/fr` conversation session times out after 10 minutes of inactivity. If the user hasn't sent a message for 10 minutes, automatically run the `/fr-stop` logic (save session state, update vocab, send summary).

Check last_message_at in session-state.json at the start of each agent turn. If it's more than 10 minutes ago and the session is still marked active, run the stop procedure instead of continuing the conversation.
