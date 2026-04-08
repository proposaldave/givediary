# Claude Code Prompt: Replace Connect Screen with Sui zkLogin

**Paste this into a Claude Code session opened in `C:\Users\dave\givediary\`**

## MISSION

Replace the "API Proxy URL" connect screen in `chat.html` with Google Sign-In via Sui zkLogin (Enoki SDK). The diary should open with two buttons: "Sign in with Google" and "Try Demo". Google sign-in creates a Sui agent for the player via Enoki. Demo mode works as-is (no changes needed to demo responses).

## WHAT EXISTS

- `chat.html` — working GiveDiary chat with pickleball voice system prompt, demo mode (18 pattern-matched responses), and Claude API integration via Cloudflare Worker proxy
- `index.html` — landing page with "Try the diary" and "Demo" buttons linking to chat.html
- The proxy URL connect screen (lines ~320-340 in chat.html) is what gets replaced

## REFERENCE IMPLEMENTATION

A working Enoki/zkLogin integration exists at:
`C:\Users\dave\CLAUDE COWORK\gnt_overnight\mnt\CLAUDE COWORK\GnT_Lab\claude_code_data\blockchain\demo\`

Key files to read:
- `src/enoki-config.ts` — has the Google Client ID and Enoki public key:
  - Enoki Public Key: `enoki_public_0a9b0cf27ea4c99aebbe0ccddd0366ac`
  - Google Client ID: `944366278531-m4vm06vgduhna14l7bmbmhjnda81ic7p.apps.googleusercontent.com`
  - Network: Sui Testnet
- `src/main.ts` — full Sui transaction logic
- `index.html` — the 5-step demo UI

## WHAT TO BUILD

### 1. Replace the connect screen in chat.html

Instead of:
```
API Proxy URL: [input field]
[Connect] [Try Demo]
```

Show:
```
GiveDiary
A private diary that talks like a player...

[Sign in with Google]  (gold button, Google icon)
[Try Demo]             (ghost button, works as-is)
```

### 2. Google Sign-In Flow

Use Enoki SDK for zkLogin:
1. User clicks "Sign in with Google"
2. Google OAuth popup/redirect
3. Enoki creates a Sui address from the Google JWT
4. Store the session (localStorage)
5. Show the chat interface with the user's name from Google
6. The Sui address becomes their agent ID (for future on-chain diary entries)

### 3. After Sign-In

- Show user's first name in the header (from Google profile)
- Show a small Sui address badge (truncated, like `0x1a3f...8c2d`)
- Chat works with Claude API via the existing proxy mechanism
- Each message could eventually be an on-chain sealed signal (future — don't build this yet)

### 4. Architecture Decision

This is a GitHub Pages static site. Options:
- **Option A (recommended):** Use Google Sign-In for Web (GSI) client library directly + Enoki REST API. No build step needed. Pure client-side JS.
- **Option B:** Convert to Vite app, use @mysten/enoki npm package. Needs build step + deploy pipeline change.

**Go with Option A** unless there's a blocking reason. Keep it as a single HTML file deployed to GitHub Pages.

### 5. Constraints

- Keep ALL existing functionality (demo mode, pickleball voice system prompt, Claude API proxy)
- Keep the GiveDiary gold/black/Playfair aesthetic
- The Google button should look native to the design (not the default Google button)
- Mobile-first — this needs to work on phones
- NEVER auto-publish to givediary.com without explicit approval. Work locally, test locally. Dave will push when ready.
- Redirect URI for OAuth: `https://givediary.com/chat.html` (for production) and `http://localhost:*` (for dev)

### 6. Testing

- Test demo mode still works (click Try Demo, type "I hate bangers", verify response)
- Test Google sign-in flow (may need to add localhost to Google OAuth redirect URIs)
- Test that sign-in state persists on page reload
- Test sign-out flow

## DONE WHEN

1. chat.html opens with "Sign in with Google" + "Try Demo" (no proxy URL screen)
2. Demo mode works exactly as before
3. Google sign-in creates a Sui address and shows the user's name
4. The design matches the GiveDiary aesthetic
5. All changes are committed locally but NOT pushed
