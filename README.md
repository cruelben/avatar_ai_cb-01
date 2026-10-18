# CB-01 · Desk Avatar

A neon CRT-style avatar that lives on your second monitor while you work. It breathes, blinks, yawns, and takes short naps. It speaks with your browser's Italian voice, reminds you to take breaks, reacts to microphone noises, and — in AI mode — answers your questions using Gemini or Groq.

**Demo:** Open `avatar_ai.html` with Chrome or Edge. Tap once to unlock audio. Press `A` or `T` for AI mode.

---

## What is it?

CB-01 is a small HTML/JS project with no dependencies, no build step, and no server. It consists of a main file (`avatar_ai.html`), an optional text file (`audio_sourge.js`) used as context for AI mode, and a configuration tool (`avatar_opzioni_6.html`).

The idea is a desktop Tamagotchi: not an assistant, not a chatbot, but a presence with some personality that keeps you company while you work, reminds you to take a break, and occasionally makes fun of you.

Thanks to a lightweight serverless proxy (Cloudflare Workers), the avatar can now safely use the Gemini or Groq API **without exposing your API key in the HTML code**. This allows you to publish the avatar on GitHub Pages without the risk of someone stealing your key.

---

## Key Features

**Desktop life**
- Breathes, blinks, yawns, sleeps for a few seconds, changes expressions.
- Random spontaneous phrases ("I'm hungry", "what a drag", "how much longer?").
- Reacts to microphone noises: jumps at sharp sounds, listens when you speak.

**Voice**
- Reads `audio_sourge.js` out loud with the `L` key.
- Uses the browser's Italian voice, with optional subtitles.

**AI Mode**
- `T` — generic chat, with a sarcastic and slightly "Roman" personality.
- `A` — **context-aware** chat: the AI answers based exclusively on the content of `audio_sourge.js`. Neutral and professional tone.
- While waiting for a response, the avatar assumes a "thoughtful" expression and twitches its eyebrows.
- The text input field opens with `Backspace` or by entering AI mode.

**Break Reminders**
- After 60 minutes of continuous work, it warns you to take a break. Then every 30 minutes, with an increasingly insistent tone.
- Greets you when you return after an absence (standby, screen lock, inactivity).

**Scheduled Messages**
- Fixed-time phrases (lunch break, end of day, etc.) defined in `SCHEDULED_EVENTS`.

---

## Shortcuts

| Key | Function |
|---|---|
| Tap / click | Greets, unlocks audio, wakes the avatar (only outside AI mode) |
| `A` | AI Mode **with context** (`audio_sourge.js`) |
| `T` | AI Mode **without context** (generic chat) |
| `Backspace` | Opens/closes the text input field in AI mode |
| `Esc` | Closes the field; if closed, opens/closes the shortcut menu |
| `L` | Reads/interrupts `audio_sourge.js` out loud |
| `S` | Subtitles on/off |
| `M` | Total audio mute (interrupts voice and turns off sounds) |
| `N` | Reactive microphone on/off |
| `D` | Shows/hides the microphone meter |

---

## Requirements

- **Chrome or Edge** (recommended). Firefox does not support the Web Speech API for voice recognition.
- A **Google Gemini API key** or **Groq API key** for AI mode.
- A free **Cloudflare** account (for the proxy).
- For the reactive microphone: open the file from `https://` or `http://localhost`. From `file://`, Chrome asks for microphone permission on every reload.

---

## API Architecture (Cloudflare Proxy)

To avoid exposing your API key in the HTML code, the project uses a proxy on Cloudflare Workers.

**How it works:**
1. Your avatar (locally or on GitHub) sends the question to your Cloudflare Worker (e.g., `https://cb01-proxy.yourname.workers.dev`).
2. The Worker receives the request, adds your secret API key (securely stored on Cloudflare's servers), and forwards it to Google (Gemini) or Groq.
3. The provider processes the response, sends it back to the Worker, which then forwards it to your avatar.

This way, anyone looking at your avatar's source code will only see the Worker URL, not your API keys.

### How to set up the Cloudflare proxy

1. **Create an account** at [Cloudflare](https://dash.cloudflare.com).
2. Go to **Workers & Pages** and click **Create application** > **Create Worker**.
3. Name your Worker (e.g., `cb01-proxy`) and click **Deploy**.
4. Click **Edit code**, paste the Worker code (provided in the repo or given to you), and click **Save and Deploy**.
5. Go to **Settings** > **Variables and Secrets**.
6. Add your "Secrets":
   - Click **Add variable** > **Secret**.
   - **Variable name**: type `GEMINI_API_KEY`.
   - **Secret value**: paste your Gemini API key.
   - Click **Save**.
   - (Optional) Repeat for Groq: **Variable name** `GROQ_API_KEY`, **Secret value** your Groq key.
7. Copy your Worker URL (e.g., `https://cb01-proxy.yourname.workers.dev`) and paste it into the `avatar_ai.html` file at the line:
   ```js
   const PROXY_BASE_URL = 'https://cb01-proxy.yourname.workers.dev';
   ```

---

## Installation (Local or GitHub)

1. **Download the files** (or clone the repo):
   ```
   git clone https://github.com/yourname/avatar_ai_cb-01.git
   cd avatar_ai_cb-01
   ```

2. **Configure the avatar**: open `avatar_ai.html` and make sure the `PROXY_BASE_URL` constant points to your Cloudflare Worker (see section above).

3. **Open the file** with Chrome or Edge. Tap once to unlock audio. Try out the shortcuts.

---

## How to prepare content for AI Mode (A)

AI mode `A` uses the content of `audio_sourge.js` as a knowledge base. You can put anything in there: a manual, notes, a technical spec, or text you want to consult by voice.

**Simplest way:** open `audio_sourge.js` with a text editor and write the content between the backticks:
```js
window.AUDIO_SOURGE = `Here goes your text.

An empty line = new paragraph.
`;
```

**Recommended way:** use the `avatar_opzioni_6.html` tool, "📖 Audio Source Editor" tab. Paste the text, export it as `audio_sourge.js`, and replace the old file.

---

## The Options Tool (`avatar_opzioni_6.html`)

This is a unified editor that lets you modify the avatar without touching the code manually. It has three tabs:
- **📅 Schedule & Phrases** — scheduled messages, reactive sound phrases, and voice reply phrases.
- **🔊 Audio & Reactivity** — voice and sound volumes, microphone thresholds.
- **📖 Audio Source Editor** — the text of `audio_sourge.js`.

Open the tool, load `avatar_ai.html`, make your changes, click "💾 GENERA INDEX.HTML", and you'll get a new file with the modifications.

---

## Customization

**Changing the chat personality (T mode).** In `avatar_ai.html`, search for:
```js
const GEMINI_SYSTEM_PROMPT = "Sei CB-01, un avatar da scrivania sarcastico...";
```

**Changing the voice tone.** Search for `utterance.rate` and `utterance.pitch` inside `speakText`.

**Changing the AI model.** Search for:
```js
const GEMINI_MODEL = 'gemini-3.5-flash-lite';
```
*(Model names change frequently: if you get an error, update this value).*

---

## Publishing on GitHub Pages

Now that the API keys are safely stored on Cloudflare, **you can publish your avatar on GitHub Pages with total peace of mind**. The HTML file contains no secrets, so GitHub won't block you and no one can steal your keys.

**Repository structure:**
```
avatar_ai_cb-01/
├── avatar_ai.html          # the complete avatar (without API keys)
├── audio_sourge.js         # the text used for AI mode and the L key
├── avatar_opzioni_6.html   # configuration editor (optional)
└── README.md               # this file
```

---

## Technical Notes

- **Canvas 2D** for drawing the avatar (no libraries).
- **Web Audio API** for electronic sounds (greeting, yawn).
- **speechSynthesis** for the Italian voice.
- **getUserMedia + AnalyserNode** for the reactive microphone (local volume analysis, no audio recorded or sent).
- **Idle Detection API** (Chromium only) to detect breaks.
- **Gemini API / Groq API** for AI mode, via Cloudflare proxy.
- **localStorage** to remember settings (muted, subtitles, microphone).

---

## What it does NOT do

- **Does not record** your voice. The reactive microphone only measures volume.
- **Does not spy on you**. Everything runs in your browser and on your personal proxy.
- **Does not replace an assistant**. It is a decorative avatar.

---

## License

MIT — do whatever you want with it, I take no responsibility.