# PickleDiary UX Spec — What Makes This Better Than ChatGPT

*v1 — April 7, 2026. Feed this to Claude to build.*

---

## The Core Insight

ChatGPT is a smart stranger. PickleDiary is a friend who was at every session you've ever played. The difference is memory, context, and the trust that comes from actual privacy.

---

## Feature 1: Total Memory

**What it does:** Every entry is stored, indexed, and queryable forever. The diary builds a complete timeline of your pickleball life — sessions, people, feelings, breakthroughs, setbacks.

**What ChatGPT can't do:** Forget. It loses everything. You explain the same context every conversation.

**How it works technically:**
- Every message the user sends is stored as a structured entry with metadata: timestamp, extracted player names, extracted topics (skill work, frustration, joy, people, goals), sentiment
- An LLM processes each entry on ingest to extract: mentioned players, session date/location if stated, emotional tone, skill references, explicit preferences
- Entries are chunked and embedded for semantic search
- A running "player profile" document is maintained per mentioned person

**User experience:**
- "My backhand still sucks" -> "You said that in February too. But in March you mentioned your drops were landing. What changed?"
- "Tell me about Mike" -> "You've mentioned Mike 12 times. Mostly frustration..."
- "How was my month?" -> summary: sessions played, people mentioned most, emotional arc, breakthroughs, recurring frustrations

**Data model per entry:**
```json
{
  "timestamp": "ISO date",
  "raw_text": "the full message",
  "extracted": {
    "players_mentioned": ["Mike", "Sarah"],
    "topics": ["play_style", "frustration"],
    "sentiment": "negative",
    "skill_references": ["banging", "dinking"],
    "session_context": { "day": "Wednesday", "time": "evening", "location": null },
    "explicit_preferences": ["avoid_mike_on_wednesdays"]
  }
}
```

**What to build:**
1. Entry processing pipeline: raw text -> LLM extraction -> structured metadata -> storage
2. Semantic search index across all entries
3. Player mention tracker with running profile per person
4. Time-based summary generator (weekly, monthly, on-demand)
5. "What did I say about X?" query handler

---

## Feature 2: Your People Map

**What it does:** Builds a private social graph of your community — entirely from YOUR perspective. Every person you mention gets a profile that accumulates over time. Nobody else sees this.

**What ChatGPT can't do:** Know who anyone in your life is.

**How it works technically:**
- Name entity recognition on every entry
- Fuzzy matching: "Mike", "Big Mike", "that tall guy Mike" -> same person
- Per-person profile accumulates: times mentioned, sentiment distribution, contexts, play style observations, affinity score
- People map is a ranked list: top = most positive, bottom = avoid

**Data model per person:**
```json
{
  "name": "Mike",
  "aliases": ["Big Mike", "Mike T"],
  "first_mentioned": "ISO date",
  "mention_count": 14,
  "sentiment_history": [
    { "date": "...", "sentiment": "negative", "context": "banging" },
    { "date": "...", "sentiment": "neutral", "context": "outdoor session" }
  ],
  "overall_sentiment": -0.6,
  "topics": ["banger", "slams", "kills rallies"],
  "days_associated": ["Wednesday"],
  "affinity": "avoid",
  "last_mentioned": "ISO date",
  "summary": "Aggressive player. Slams everything. 1 good session out of 12 mentions. Mostly avoid."
}
```

**What to build:**
1. Name extraction + fuzzy dedup pipeline
2. Per-person profile store with running updates
3. Sentiment tracking per person over time
4. Affinity classification (love -> avoid spectrum)
5. "Who should I play with?" recommendation engine based on people map
6. New person detection + proactive follow-up

---

## Feature 3: Pattern Engine

**What it does:** Surfaces insights the player can't see themselves. Reads across weeks and months.

**What ChatGPT can't do:** Analyze anything across sessions. It has no history.

**Pattern types:**

- Temporal: "You're frustrated after Wednesday sessions 4 out of the last 5 weeks."
- People: "Every session with Sarah gets a positive entry."
- Skill: "Your backhand mentions went from 8 (all negative) in January to 1 (positive) in March. Improving faster than you think."
- Emotional: "You're hardest on yourself after competitive events."

**How insights surface (UX):**
- As conversation, NOT dashboards or reports
- Diary volunteers insights naturally — as a friend noticing something
- "Last Wednesday was tough. How was this one?"
- "I'm noticing something. Want to hear it?"

**What to build:**
1. Weekly pattern scan surfacing 1-2 insights
2. Temporal correlation engine: day x sentiment, time x sentiment, people x sentiment
3. Skill mention tracker with trajectory
4. Proactive nudges woven into conversation
5. Longitudinal awareness

---

## Feature 5: Actually Private

**How PickleDiary is different:**
- All diary entries encrypted with keys derived from Google login via Sui zkLogin
- Encryption happens in the browser BEFORE data leaves the device
- Server stores ciphertext — unreadable without user's key
- Even the team cannot decrypt — architectural, not policy
- Delete account = permanent, unrecoverable

**Trust-building moments (UX):**
- First entry: "Everything you write here is locked with a key only you have. Not even we can read it."
- First vulnerable entry: "That took courage to write. No one will ever see this but you."
- After 10 entries: "You've written 10 entries. Every one is private. This is your space."
- Never again after that — trust is built

**What to build:**
1. Client-side Seal encryption (replace placeholder)
2. Vulnerability detection -> gentle privacy reinforcement
3. Onboarding flow in human language
4. Settings page with simple visual explainer
5. Account deletion flow

---

## Feature 7: Session Ritual

Post-session flow: how'd it go -> who'd you play with -> highlight -> anything bugging you -> done. 2 minutes. Becomes habit like stretching after a workout.

## Feature 8: Your Pickleball Story

After enough entries, generate YOUR STORY — a narrative of your journey across months. Longitudinal narrative generator.

## Feature 9: The Mirror

Contradiction detector — "You say you want to improve, but you haven't mentioned practicing once in 6 weeks." Not judgmental. Honest. Like a good friend who notices things.

## Feature 10: Vocabulary That Grows With You

Match player's vocabulary level. A 3.0 says "that fast shot." A 4.5 says "inside-out speed-up off the bounce." Introduce terms naturally as they advance.

---

## Priority Order for Building

1. **Memory** (#1) — without this, it's just ChatGPT. Foundation.
2. **People Map** (#2) — most emotionally resonant. "It knows my people."
3. **Privacy** (#5) — real Seal encryption. Replaces placeholder.
4. **Pattern Engine** (#3) — needs enough entries. Build after memory accumulates.
5. **Pickleball Voice** (#6) — separate spec (PICKLEDIARY_VOICE_SPEC.md)
6. **Session Ritual** (#7) — already prototyped, sharpen
7. **Your Story** (#8) — unlock after 30+ entries
8. **The Mirror** (#9) — unlock after 20+ entries
9. **Growing Vocabulary** (#10) — ongoing, tied to #6
