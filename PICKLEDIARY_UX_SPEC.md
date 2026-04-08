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
- Entries are chunked and embedded for semantic search — "what did I say about my backhand?" retrieves every backhand mention across months
- A running "player profile" document is maintained per mentioned person — updated after every entry that mentions them

**User experience:**
- User says: "My backhand still sucks"
- Diary responds: "You said that in February too. But in March you mentioned your drops were landing. What changed?"
- User says: "Tell me about Mike"
- Diary responds: "You've mentioned Mike 12 times. Mostly frustration — he slams too much, kills the rally. You had one good session with him in March when you played outdoors. You haven't mentioned him in 3 weeks."
- User says: "How was my month?"
- Diary responds with a summary: sessions played, people mentioned most, emotional arc, breakthroughs noted, recurring frustrations

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

**What it does:** Builds a private social graph of your community — entirely from YOUR perspective. Every person you mention gets a profile that accumulates over time. Nobody else sees this. It's YOUR view of YOUR people.

**What ChatGPT can't do:** Know who anyone in your life is.

**How it works technically:**
- Name entity recognition on every entry — extract player names, nicknames, pronouns resolved to names when possible
- Fuzzy matching: "Mike", "Big Mike", "that tall guy Mike" -> same person
- Per-person profile accumulates: times mentioned, sentiment distribution, contexts (which days, which groups), what you've said about them, play style observations, affinity score (derived from how you talk about them)
- The people map is a ranked list: top = people you talk about most positively, bottom = people you avoid

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
  "summary": "Aggressive player. Slams everything. You've had 1 good session out of 12 mentions. Mostly avoid."
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

**What it does:** Surfaces insights the player can't see themselves — because they don't have the data or the perspective. The diary reads across weeks and months and finds what the player is too close to notice.

**What ChatGPT can't do:** Analyze anything across sessions. It has no history.

**Pattern types to detect:**

**Temporal patterns:**
- "You're frustrated after Wednesday sessions 4 out of the last 5 weeks."
- "You play your best when you mention playing in the morning."
- "You haven't played in 8 days."

**People patterns:**
- "Every session with Sarah gets a positive entry."
- "You mention frustration with Mike only when you're in the same group."
- "You've mentioned wanting to play with better players 4 times but haven't signed up for the 4.0 group."

**Skill patterns:**
- "You mentioned your backhand 8 times in January (all negative), 3 times in February (mixed), once in March (positive). It's improving faster than you think."
- "You bring up dinking when you're happy and banging when you're frustrated."

**Emotional patterns:**
- "Your entries are more positive when you play with 3 specific people."
- "You're hardest on yourself after competitive events."

**How insights surface (UX):**
- NOT as a dashboard or report. As conversation.
- Diary volunteers insights naturally — not as a report, as a friend noticing something
- User can ask: "Any patterns you see?"

**What to build:**
1. Weekly pattern scan across all entries surfacing 1-2 insights
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
- Even the team cannot decrypt entries — architectural, not policy
- Deleting account deletes encrypted blobs permanently

**Trust-building moments (UX):**
- First entry: explain once, briefly
- First vulnerable entry: reinforce gently
- After 10 entries: "You've written 10 entries. Every one is private."
- Never again after that

---

## Feature 7: Session Ritual

Post-session flow: how'd it go -> who'd you play with -> highlight -> anything bugging you -> done. 2 minutes. Becomes habit.

## Feature 8: Your Pickleball Story

After enough entries, generate YOUR STORY — a narrative of your journey. Longitudinal narrative generator.

## Feature 9: The Mirror

Contradiction detector — compares stated goals to actual behavior. Surfaces gently.

## Feature 10: Daily Audio Briefing (DAY 1 FEATURE)

5-10 minute AI audio briefing. Listen on drive to pickleball. ElevenLabs TTS.

**Build order:**
1. Daily briefing generator via TTS (generate text -> ElevenLabs API -> audio)
2. Voice input option (speech-to-text -> process as text entry)
3. Full voice conversation mode (ElevenLabs Conversational AI)

## Feature 11: Vocabulary That Grows With You

Match player's vocabulary level. Introduce terms naturally as they advance.

---

## Priority Order for Building

**DAY 1 (launch features):**
1. Memory (#1) — foundation
2. People Map (#2) — emotionally resonant
3. Daily Audio Briefing (#10) — word of mouth driver
4. Privacy (#5) — real Seal encryption
5. Pickleball Voice (#6)

**WEEK 2+:**
6. Pattern Engine (#3)
7. Session Ritual (#7)
8. Voice Conversation Mode (#10 Mode 2)

**MONTH 2+:**
9. Your Story (#8)
10. The Mirror (#9)
11. Growing Vocabulary (#11)
