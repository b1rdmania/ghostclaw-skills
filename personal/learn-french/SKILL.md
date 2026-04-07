---
name: learn-french
description: Personal French language learning system built on Tim Ferriss's DiSSS/CaFE method. Sets up the Deconstruction Dozen session, daily 8am vocab drills with spaced repetition, on-demand conversation practice, survival phrases, stakes tracking, and a progress tracker. Triggers on "learn french", "french setup", "start french", "/french-start", "/fr-start".
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
  "deconstruction_done": false,
  "goal": null,
  "deadline": null,
  "goal_set_date": null
}
```

Create `$GHOSTCLAW_GROUP_DIR/french/vocab-progress.json` if it doesn't exist, seeded with the top 200 French frequency words (see **Starter Word List** section below). Each entry:

```json
[
  {
    "rank": 1,
    "word": "le",
    "translation": "the (masc.)",
    "ipa": "/lə/",
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

Commands: `/fr-start` (or `/french-start`), `/fr`, `/fr-stop`, `/fr-stats`, `/fr-pronounce`, `/fr-goal`

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

**Voice note handling:**
- When the user sends a voice note during a `/fr` session, GhostClaw's transcription system (ElevenLabs Scribe) automatically transcribes it — the agent receives it as text. Treat that transcribed text as the user's French attempt.
- After generating a French response, send two messages: first send the French text via `mcp__ghostclaw__send_message`, then send a second message containing only `<tts>FRENCH RESPONSE HERE</tts>` so ElevenLabs speaks it aloud. This creates a real voice loop: speak French → transcribed → Claude responds in French → hear it spoken back.
- If the user sends a voice note, after the main response add a brief pronunciation note for 1–2 words they likely mispronounced based on common English-speaker errors with those French words. Format: `*(Prononciation: "vous" → /vu/, not /voʊz/)*`

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
[progress bar: █████░░░░░ 47%]

*What you can do now*: [milestone label — see below]
```

Progress % = (known_count / 1200) * 100, capped at 100.

Progress bar: 10 blocks, filled = floor(pct / 10), empty = 10 - filled.

**Capability milestones** (pick the highest one the user has reached):
| Known words | Milestone label |
|-------------|----------------|
| 0–49 | Just getting started |
| 50–99 | Can handle greetings and simple introductions |
| 100–199 | Can order food, ask for directions, and manage basic transactions |
| 200–399 | Can talk about yourself, your work, and daily life |
| 400–599 | Can handle most everyday situations without preparation |
| 600–899 | Can discuss opinions, news, and some abstract topics |
| 900–1199 | Near conversational — only gaps in specific vocabulary |
| 1200+ | Conversational fluency (B1–B2) ✓ |

If `goal` is set in stats.json, also show:
```
*Goal*: [goal]
*Deadline*: [date] ([N] days away)
*Pace needed*: [words_per_day] words/day to reach 1,200 in time
```

---

### Daily Vocab Drill (cron, 8am)

This runs automatically at 8am every day.

**Algorithm:**
1. Read `french/vocab-progress.json`
2. Collect **due words**: status `learning` AND `next_review <= today`. Take up to 20.
3. Collect **new words**: status `new`, sorted by rank (lowest rank first). Take enough to reach 10 total (10 - due_count, minimum 0).
4. If 0 due + 0 new: send "No drill today — you're all caught up! Use /fr to practice conversation."
5. Otherwise, send a drill via Telegram.

**Drill format:**

For each word in the drill, randomly assign a direction: 🔊 (listen-first) or 👁️ (read-first). Alternate between both across the session so roughly half go each way.

- **🔊 listen-first**: Send `<tts>WORD</tts>` as a standalone message so ElevenLabs speaks it, then send "What does this mean?" — the user hears the word and recalls its meaning.
- **👁️ read-first**: Show the word in bold — the user sees it and recalls its meaning. No TTS for these.

Send the drill header first:

```
*French Drill — [date]*

🔊 = listen and recall the meaning  👁️ = read and recall the meaning

Reply with the numbers you got right (e.g. `1 3 5`) or `all` / `none`.
```

Then for each word:
- 🔊 words: send `<tts>WORD</tts>`, then send `[N]. 🔊 ___` (blank so they hear and recall)
- 👁️ words: send `[N]. 👁️ *WORD*` (they see it and recall)

Example sequence:
```
<tts>le</tts>
1. 🔊 ___

2. 👁️ *être*

<tts>grand</tts>
3. 🔊 ___
```

Then send a follow-up if not already included: "Reply with the numbers you got right (e.g. `1 3 5`) or `all` / `none`."

**After user replies:**

Before updating scores, send the answer reveal: list each word with its translation and IPA:
```
*Answers:*
1. *le* — the (masc. sing.) — /lə/
2. *avoir* — to have — /avwaʁ/
...
```
Show all words from the drill (both correct and incorrect). Format: `[N]. *word* — translation — /ipa/`

- For each word marked correct: double interval_days (cap at 30), set next_review = today + interval_days, update correct_streak; if correct_streak >= 3, set status `known`
- For each word marked wrong: set interval_days = 1, next_review = tomorrow, set correct_streak = 0
- Save updated vocab-progress.json
- Update stats.json: update last_active, recalculate streak
- Send confirmation: "Drill done. [N] known, [N] review tomorrow."

**Streak calculation:**
- If last_active was yesterday or today: streak += 1
- If last_active was before yesterday: streak = 1
- Update last_active to today

---

### `/fr-pronounce [word or phrase]`

Can be used anytime — no active `/fr` session required.

1. Send `<tts>WORD OR PHRASE</tts>` so ElevenLabs speaks it aloud.
2. Also send the IPA in text: `*WORD* — /IPA/` (e.g. `*bonjour* — /bɔ̃ʒuʁ/`). Look up IPA from vocab-progress.json if the word is in the list (use its `"ipa"` field). If it's not in the list, derive IPA from standard French phonology rules.
3. If the word is found in `french/vocab-progress.json`, also show its current status: `*(Status: learning — next review: [date])*`

Example response for `/fr-pronounce bonjour`:
```
<tts>bonjour</tts>
*bonjour* — /bɔ̃ʒuʁ/
*(Not yet in your word list)*
```

---

### `/fr-goal [your goal] by [date]` — Set Stakes

Ferriss's Stakes principle: real consequences accelerate learning.

Parse the user's message for a goal description and a deadline date. Examples:
- `/fr-goal hold a 10-minute conversation by June 1`
- `/fr-goal order food and navigate Paris by May 15`
- `/fr-goal pass a B1 test by July 2026`

1. Read `french/stats.json`
2. Update `goal`, `deadline` (ISO date string), and `goal_set_date` (today)
3. Calculate days remaining
4. Send confirmation:

```
*Stakes set* 🎯

Goal: [goal]
Deadline: [date] ([N] days away)

At your current pace you need [N] words to hit conversational fluency.
You're at [known_count] / 1,200.

To hit your deadline: aim for [words_per_day] new words per day.
```

If no deadline is parseable, ask the user: "What date are you aiming for? (e.g. 'by June 1' or 'in 8 weeks')"

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

### 4. Create survival-phrases.json

These are the 20 highest-value phrases for social situations — useful from day one. Write `$GHOSTCLAW_GROUP_DIR/french/survival-phrases.json`:

```json
[
  {"id":1,"phrase":"Bonjour !","translation":"Hello! / Good morning!","context":"greeting"},
  {"id":2,"phrase":"Bonsoir !","translation":"Good evening!","context":"greeting"},
  {"id":3,"phrase":"Comment vous appelez-vous ?","translation":"What's your name? (formal)","context":"introduction"},
  {"id":4,"phrase":"Je m'appelle [name].","translation":"My name is [name].","context":"introduction"},
  {"id":5,"phrase":"Enchanté(e) !","translation":"Nice to meet you!","context":"introduction"},
  {"id":6,"phrase":"Comment ça va ?","translation":"How are you? (informal)","context":"greeting"},
  {"id":7,"phrase":"Ça va bien, merci.","translation":"I'm doing well, thank you.","context":"greeting"},
  {"id":8,"phrase":"S'il vous plaît.","translation":"Please. (formal)","context":"politeness"},
  {"id":9,"phrase":"Merci beaucoup.","translation":"Thank you very much.","context":"politeness"},
  {"id":10,"phrase":"De rien.","translation":"You're welcome.","context":"politeness"},
  {"id":11,"phrase":"Excusez-moi.","translation":"Excuse me. (formal)","context":"politeness"},
  {"id":12,"phrase":"Je ne comprends pas.","translation":"I don't understand.","context":"communication"},
  {"id":13,"phrase":"Pouvez-vous répéter, s'il vous plaît ?","translation":"Can you repeat that, please?","context":"communication"},
  {"id":14,"phrase":"Parlez-vous anglais ?","translation":"Do you speak English?","context":"communication"},
  {"id":15,"phrase":"Je parle un peu français.","translation":"I speak a little French.","context":"communication"},
  {"id":16,"phrase":"Où sont les toilettes ?","translation":"Where are the toilets?","context":"practical"},
  {"id":17,"phrase":"L'addition, s'il vous plaît.","translation":"The bill, please.","context":"restaurant"},
  {"id":18,"phrase":"Je voudrais [item], s'il vous plaît.","translation":"I would like [item], please.","context":"ordering"},
  {"id":19,"phrase":"Combien ça coûte ?","translation":"How much does it cost?","context":"shopping"},
  {"id":20,"phrase":"Au revoir !","translation":"Goodbye!","context":"farewell"}
]
```

During `/fr` conversation sessions, occasionally work survival phrases naturally into the conversation (especially early on when vocab is thin). When the user uses one correctly, note it with `*(Survival phrase mastered ✓)*`.

---

### 5. Seed vocab-progress.json with the starter word list

Write `$GHOSTCLAW_GROUP_DIR/french/vocab-progress.json` with the following data. This is the top 200 most frequent French words by corpus frequency. The full 1,200-word list can be found at: https://en.wiktionary.org/wiki/Wiktionary:Frequency_lists/French_wordlist — add those words in rank order (201–1200) with status `new` to extend the list.

```json
[
  {"rank":1,"word":"le","translation":"the (masc. sing.)","ipa":"/lə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":2,"word":"de","translation":"of / from","ipa":"/də/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":3,"word":"un","translation":"a / one (masc.)","ipa":"/œ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":4,"word":"être","translation":"to be","ipa":"/ɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":5,"word":"et","translation":"and","ipa":"/e/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":6,"word":"à","translation":"at / to / in","ipa":"/a/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":7,"word":"il","translation":"he / it (masc.)","ipa":"/il/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":8,"word":"avoir","translation":"to have","ipa":"/avwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":9,"word":"ne","translation":"not (negative particle)","ipa":"/nə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":10,"word":"je","translation":"I","ipa":"/ʒə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":11,"word":"son","translation":"his / her / its (masc.)","ipa":"/sɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":12,"word":"que","translation":"that / what / which","ipa":"/kə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":13,"word":"se","translation":"oneself (reflexive)","ipa":"/sə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":14,"word":"qui","translation":"who / which","ipa":"/ki/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":15,"word":"ce","translation":"this / that / it","ipa":"/sə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":16,"word":"en","translation":"in / of it / some","ipa":"/ɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":17,"word":"du","translation":"of the (masc.) / some","ipa":"/dy/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":18,"word":"elle","translation":"she / it (fem.)","ipa":"/ɛl/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":19,"word":"au","translation":"at the / to the (masc.)","ipa":"/o/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":20,"word":"des","translation":"of the (pl.) / some","ipa":"/de/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":21,"word":"les","translation":"the (pl.)","ipa":"/le/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":22,"word":"pas","translation":"not (with ne)","ipa":"/pɑ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":23,"word":"on","translation":"one / we (informal)","ipa":"/ɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":24,"word":"plus","translation":"more / no more","ipa":"/ply/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":25,"word":"par","translation":"by / through / per","ipa":"/paʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":26,"word":"mais","translation":"but","ipa":"/mɛ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":27,"word":"vous","translation":"you (formal / pl.)","ipa":"/vu/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":28,"word":"nous","translation":"we / us","ipa":"/nu/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":29,"word":"lui","translation":"him / to him/her","ipa":"/lɥi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":30,"word":"si","translation":"if / yes (contradiction)","ipa":"/si/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":31,"word":"tout","translation":"all / everything","ipa":"/tu/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":32,"word":"bien","translation":"well / good","ipa":"/bjɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":33,"word":"faire","translation":"to do / to make","ipa":"/fɛʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":34,"word":"sur","translation":"on / about / over","ipa":"/syʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":35,"word":"leur","translation":"their / to them","ipa":"/lœʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":36,"word":"grand","translation":"big / great / tall","ipa":"/ɡʁɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":37,"word":"avec","translation":"with","ipa":"/avɛk/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":38,"word":"non","translation":"no / not","ipa":"/nɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":39,"word":"pouvoir","translation":"to be able to / can","ipa":"/puvwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":40,"word":"même","translation":"same / even / itself","ipa":"/mɛm/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":41,"word":"aussi","translation":"also / too","ipa":"/osi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":42,"word":"dans","translation":"in / inside","ipa":"/dɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":43,"word":"ou","translation":"or","ipa":"/u/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":44,"word":"y","translation":"there / to it","ipa":"/i/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":45,"word":"comme","translation":"as / like / how","ipa":"/kɔm/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":46,"word":"dont","translation":"of which / whose","ipa":"/dɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":47,"word":"autre","translation":"other / another","ipa":"/otʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":48,"word":"peu","translation":"little / few","ipa":"/pø/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":49,"word":"homme","translation":"man / human","ipa":"/ɔm/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":50,"word":"vouloir","translation":"to want","ipa":"/vulwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":51,"word":"dire","translation":"to say / to tell","ipa":"/diʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":52,"word":"temps","translation":"time / weather","ipa":"/tɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":53,"word":"très","translation":"very","ipa":"/tʁɛ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":54,"word":"savoir","translation":"to know (facts/how)","ipa":"/savwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":55,"word":"falloir","translation":"to be necessary (il faut)","ipa":"/falwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":56,"word":"voir","translation":"to see","ipa":"/vwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":57,"word":"chose","translation":"thing","ipa":"/ʃoz/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":58,"word":"quand","translation":"when","ipa":"/kɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":59,"word":"aller","translation":"to go","ipa":"/ale/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":60,"word":"encore","translation":"still / again / even","ipa":"/ɑ̃kɔʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":61,"word":"la","translation":"the (fem. sing.)","ipa":"/la/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":62,"word":"où","translation":"where","ipa":"/u/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":63,"word":"oui","translation":"yes","ipa":"/wi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":64,"word":"jour","translation":"day","ipa":"/ʒuʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":65,"word":"deux","translation":"two","ipa":"/dø/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":66,"word":"bon","translation":"good (masc.)","ipa":"/bɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":67,"word":"nouveau","translation":"new (masc.)","ipa":"/nuvo/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":68,"word":"premier","translation":"first (masc.)","ipa":"/pʁəmje/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":69,"word":"venir","translation":"to come","ipa":"/vəniʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":70,"word":"prendre","translation":"to take","ipa":"/pʁɑ̃dʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":71,"word":"vie","translation":"life","ipa":"/vi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":72,"word":"après","translation":"after / afterwards","ipa":"/apʁɛ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":73,"word":"sous","translation":"under / below","ipa":"/su/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":74,"word":"rien","translation":"nothing","ipa":"/ʁjɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":75,"word":"depuis","translation":"since / for (time)","ipa":"/dəpɥi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":76,"word":"donner","translation":"to give","ipa":"/dɔne/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":77,"word":"toujours","translation":"always / still","ipa":"/tuʒuʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":78,"word":"an","translation":"year","ipa":"/ɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":79,"word":"avant","translation":"before / in front","ipa":"/avɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":80,"word":"alors","translation":"then / so","ipa":"/alɔʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":81,"word":"car","translation":"because / for (written)","ipa":"/kaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":82,"word":"là","translation":"there / here","ipa":"/la/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":83,"word":"sans","translation":"without","ipa":"/sɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":84,"word":"monde","translation":"world / people","ipa":"/mɔ̃d/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":85,"word":"contre","translation":"against","ipa":"/kɔ̃tʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":86,"word":"déjà","translation":"already","ipa":"/deʒa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":87,"word":"moi","translation":"me / I (emphatic)","ipa":"/mwa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":88,"word":"toi","translation":"you (emphatic / informal)","ipa":"/twa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":89,"word":"heure","translation":"hour / time (o'clock)","ipa":"/œʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":90,"word":"passer","translation":"to pass / to spend (time)","ipa":"/pɑse/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":91,"word":"France","translation":"France","ipa":"/fʁɑ̃s/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":92,"word":"entre","translation":"between / among","ipa":"/ɑ̃tʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":93,"word":"mettre","translation":"to put / to place","ipa":"/mɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":94,"word":"main","translation":"hand","ipa":"/mɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":95,"word":"part","translation":"part / share / aside","ipa":"/paʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":96,"word":"enfant","translation":"child","ipa":"/ɑ̃fɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":97,"word":"rendre","translation":"to give back / to render","ipa":"/ʁɑ̃dʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":98,"word":"demander","translation":"to ask / to request","ipa":"/dəmɑ̃de/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":99,"word":"seul","translation":"alone / only / single","ipa":"/sœl/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":100,"word":"mieux","translation":"better / best","ipa":"/mjø/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":101,"word":"pays","translation":"country","ipa":"/pei/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":102,"word":"parler","translation":"to speak / to talk","ipa":"/paʁle/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":103,"word":"trouver","translation":"to find","ipa":"/tʁuve/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":104,"word":"femme","translation":"woman / wife","ipa":"/fam/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":105,"word":"politique","translation":"politics / political","ipa":"/pɔlitik/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":106,"word":"certain","translation":"certain / sure / some","ipa":"/sɛʁtɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":107,"word":"devoir","translation":"to have to / must / duty","ipa":"/dəvwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":108,"word":"point","translation":"point / dot / not at all","ipa":"/pwɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":109,"word":"fois","translation":"time (occasion)","ipa":"/fwa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":110,"word":"eau","translation":"water","ipa":"/o/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":111,"word":"cas","translation":"case / situation","ipa":"/kɑ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":112,"word":"peut-être","translation":"maybe / perhaps","ipa":"/pøtɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":113,"word":"monsieur","translation":"Mister / sir / gentleman","ipa":"/məsjø/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":114,"word":"comprendre","translation":"to understand","ipa":"/kɔ̃pʁɑ̃dʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":115,"word":"pendant","translation":"during / for (time span)","ipa":"/pɑ̃dɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":116,"word":"place","translation":"place / seat / square","ipa":"/plas/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":117,"word":"dernier","translation":"last / latest","ipa":"/dɛʁnje/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":118,"word":"ami","translation":"friend","ipa":"/ami/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":119,"word":"état","translation":"state / condition","ipa":"/eta/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":120,"word":"travail","translation":"work / job","ipa":"/tʁavaj/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":121,"word":"connaître","translation":"to know (person/place)","ipa":"/kɔnɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":122,"word":"suivre","translation":"to follow","ipa":"/sɥivʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":123,"word":"penser","translation":"to think","ipa":"/pɑ̃se/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":124,"word":"trop","translation":"too much / too many","ipa":"/tʁo/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":125,"word":"gouvernement","translation":"government","ipa":"/ɡuvɛʁnəmɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":126,"word":"sembler","translation":"to seem / to appear","ipa":"/sɑ̃ble/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":127,"word":"tenir","translation":"to hold / to keep","ipa":"/təniʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":128,"word":"ville","translation":"city / town","ipa":"/vil/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":129,"word":"devant","translation":"in front of / before","ipa":"/dəvɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":130,"word":"nuit","translation":"night","ipa":"/nɥi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":131,"word":"mère","translation":"mother","ipa":"/mɛʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":132,"word":"père","translation":"father","ipa":"/pɛʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":133,"word":"couleur","translation":"colour","ipa":"/kulœʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":134,"word":"mort","translation":"death / dead","ipa":"/mɔʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":135,"word":"question","translation":"question","ipa":"/kɛstjɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":136,"word":"donc","translation":"so / therefore / thus","ipa":"/dɔ̃k/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":137,"word":"corps","translation":"body","ipa":"/kɔʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":138,"word":"appeler","translation":"to call","ipa":"/aple/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":139,"word":"valeur","translation":"value / worth","ipa":"/valœʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":140,"word":"milieu","translation":"middle / environment","ipa":"/miljø/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":141,"word":"manger","translation":"to eat","ipa":"/mɑ̃ʒe/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":142,"word":"force","translation":"strength / force","ipa":"/fɔʁs/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":143,"word":"entendre","translation":"to hear / to understand","ipa":"/ɑ̃tɑ̃dʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":144,"word":"rester","translation":"to stay / to remain","ipa":"/ʁɛste/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":145,"word":"laisser","translation":"to leave / to let","ipa":"/lɛse/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":146,"word":"moment","translation":"moment / time","ipa":"/mɔmɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":147,"word":"beaucoup","translation":"a lot / many / much","ipa":"/boku/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":148,"word":"tête","translation":"head","ipa":"/tɛt/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":149,"word":"partir","translation":"to leave / to go away","ipa":"/paʁtiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":150,"word":"parce que","translation":"because","ipa":"/paʁskə/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":151,"word":"ligne","translation":"line","ipa":"/liɲ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":152,"word":"ouvrir","translation":"to open","ipa":"/uvʁiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":153,"word":"semaine","translation":"week","ipa":"/səmɛn/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":154,"word":"soir","translation":"evening","ipa":"/swaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":155,"word":"matin","translation":"morning","ipa":"/matɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":156,"word":"livre","translation":"book / pound (currency)","ipa":"/livʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":157,"word":"nombre","translation":"number","ipa":"/nɔ̃bʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":158,"word":"face","translation":"face / opposite","ipa":"/fas/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":159,"word":"marcher","translation":"to walk / to work (function)","ipa":"/maʁʃe/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":160,"word":"social","translation":"social","ipa":"/sɔsjal/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":161,"word":"position","translation":"position","ipa":"/pozisjɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":162,"word":"regarder","translation":"to look at / to watch","ipa":"/ʁəɡaʁde/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":163,"word":"produire","translation":"to produce","ipa":"/pʁɔdɥiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":164,"word":"moyen","translation":"means / average / medium","ipa":"/mwajɛ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":165,"word":"retourner","translation":"to return / to go back","ipa":"/ʁətuʁne/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":166,"word":"porte","translation":"door / gate","ipa":"/pɔʁt/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":167,"word":"droit","translation":"right / straight / law","ipa":"/dʁwa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":168,"word":"guerre","translation":"war","ipa":"/ɡɛʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":169,"word":"bout","translation":"end / tip / bit","ipa":"/bu/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":170,"word":"ici","translation":"here","ipa":"/isi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":171,"word":"arriver","translation":"to arrive / to happen","ipa":"/aʁive/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":172,"word":"âge","translation":"age","ipa":"/aʒ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":173,"word":"accord","translation":"agreement / chord","ipa":"/akɔʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":174,"word":"sortir","translation":"to go out / to exit","ipa":"/sɔʁtiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":175,"word":"répondre","translation":"to answer / to reply","ipa":"/ʁepɔ̃dʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":176,"word":"jamais","translation":"never / ever","ipa":"/ʒamɛ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":177,"word":"haut","translation":"high / tall / top","ipa":"/o/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":178,"word":"long","translation":"long","ipa":"/lɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":179,"word":"vrai","translation":"true / real","ipa":"/vʁɛ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":180,"word":"propre","translation":"own / clean","ipa":"/pʁɔpʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":181,"word":"pied","translation":"foot","ipa":"/pje/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":182,"word":"ordre","translation":"order / command","ipa":"/ɔʁdʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":183,"word":"côté","translation":"side / next to","ipa":"/kote/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":184,"word":"nom","translation":"name / noun","ipa":"/nɔ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":185,"word":"ainsi","translation":"thus / so / in this way","ipa":"/ɛ̃si/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":186,"word":"forme","translation":"form / shape","ipa":"/fɔʁm/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":187,"word":"exemple","translation":"example","ipa":"/ɛɡzɑ̃pl/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":188,"word":"mois","translation":"month","ipa":"/mwa/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":189,"word":"lettre","translation":"letter","ipa":"/lɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":190,"word":"mourir","translation":"to die","ipa":"/muʁiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":191,"word":"finir","translation":"to finish / to end","ipa":"/finiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":192,"word":"courir","translation":"to run","ipa":"/kuʁiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":193,"word":"chaque","translation":"each / every","ipa":"/ʃak/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":194,"word":"travers","translation":"across / through (à travers)","ipa":"/tʁavɛʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":195,"word":"parmi","translation":"among","ipa":"/paʁmi/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":196,"word":"tellement","translation":"so much / so many","ipa":"/tɛlmɑ̃/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":197,"word":"sentir","translation":"to feel / to smell","ipa":"/sɑ̃tiʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":198,"word":"devenir","translation":"to become","ipa":"/dəvniʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":199,"word":"croire","translation":"to believe / to think","ipa":"/kʁwaʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0},
  {"rank":200,"word":"permettre","translation":"to allow / to permit","ipa":"/pɛʁmɛtʁ/","status":"new","next_review":null,"interval_days":1,"correct_streak":0}
]
```

**To extend to 1,200 words:**
Get the full Wiktionary French frequency list at https://en.wiktionary.org/wiki/Wiktionary:Frequency_lists/French_wordlist and append words ranked 201–1200 in the same JSON format (status: "new", next_review: null, interval_days: 1, correct_streak: 0).

### 6. Confirm setup

Tell the user:

> "French learning system is ready. Here's how to use it:
>
> *Step 1 (do this first):*
> `/fr-start` — Deconstruction Dozen (~20 min, unlocks your grammar profile)
>
> *Step 2 (optional but recommended):*
> `/fr-goal hold a 10-minute conversation by June 1` — set a deadline for real stakes
>
> *Daily use:*
> `/fr` — start a conversation session in French
> `/fr-stop` — end session + save new vocabulary
> `/fr-stats` — see progress + capability milestone
> `/fr-pronounce [word]` — hear any word + IPA
>
> 20 survival phrases loaded (greetings, ordering, getting around) — these will come up naturally in conversation practice.
>
> Daily 10-word drills start at 8am. First one arrives tomorrow morning."

## Method Reference

This skill implements Tim Ferriss's DiSSS/CaFE framework:

| Principle | Implementation |
|-----------|---------------|
| **Deconstruction** | Deconstruction Dozen (12 sentences unlock grammar system) |
| **Selection (80/20)** | Top 1,200 frequency words = conversational fluency threshold |
| **Sequencing** | Grammar first (day 1) → core 100 → build to 1,200 |
| **Stakes** | `/fr-goal` — set a real deadline; stats show pace needed to hit it |
| **Compression** | grammar-profile.md = one-page personal cheat sheet |
| **Frequency** | Daily 10-word drills + conversation sessions |
| **Encoding** | Krashen i+1 in conversation (new words in context, not isolation) |
| **Survival phrases** | 20 high-value social phrases usable from day one |

Target: **B1–B2 level** (conversational competency) at ~1,200 known words.

## Session auto-timeout

The `/fr` conversation session times out after 10 minutes of inactivity. If the user hasn't sent a message for 10 minutes, automatically run the `/fr-stop` logic (save session state, update vocab, send summary).

Check last_message_at in session-state.json at the start of each agent turn. If it's more than 10 minutes ago and the session is still marked active, run the stop procedure instead of continuing the conversation.

## Voice Architecture

**This skill uses async voice** (Telegram voice notes in, ElevenLabs TTS out). This is the right approach for a skill-based learning system — no infrastructure overhead, works immediately with existing GhostClaw setup.

**LiveKit real-time audio is reserved for the app** (Decipher). Real-time bidirectional audio via LiveKit is the hero feature of the standalone app — `/fr-live` → LiveKit room → browser link → live conversation. Don't build it here.

Async is better for drilling anyway (you can pause, think, replay). Real-time is better for simulated immersion conversations. Build both, in the right place.
