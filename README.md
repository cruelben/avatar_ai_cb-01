# CB-01 · Desk Avatar

A neon CRT-style avatar that lives on your desktop while you work. It breathes, blinks, yawns, and takes short naps. It speaks with your browser's Italian voice, reminds you to take breaks, reacts to microphone noises, and — in AI mode — answers your questions using Gemini.

**Version:** v1.0.2  
**Repository:** [https://github.com/cruelben/avatar_ai_cb-01](https://github.com/cruelben/avatar_ai_cb-01)

---

## What is it?

CB-01 is a small HTML/JS project with no dependencies, no build step, and no server. It consists of a main file (`avatar_ai.html`), an optional text file (`audio_sourge.js`) used as the default context for AI mode, and a configuration tool.

The idea is a desktop Tamagotchi: not an assistant, not a chatbot, but a presence with some personality that keeps you company while you work, reminds you to take a break, and occasionally makes fun of you.

Thanks to a lightweight serverless proxy (Cloudflare Workers), the avatar can safely use the Gemini API **without exposing your API key in the HTML code**. This allows you to publish the avatar on GitHub Pages without the risk of someone stealing your key.

---

## Key Features

**Desktop Life**
- Breathes, blinks, yawns, sleeps for a few seconds, changes expressions.
- Random spontaneous phrases ("I'm hungry", "what a drag", "how much longer?").
- Reacts to microphone noises: jumps at sharp sounds, listens when you speak.

**On-Canvas Controls**
All primary interactions are drawn directly on the canvas:
- **Left Column (Centered):** `A` (AI with context), `T` (AI without context), `L` (Read).
- **Right Column (Centered):** `S` (Subtitles), `M` (Mute), `N` (Listen/Microphone).
- **Top Right:** `📁 OPEN FILE` (import a custom context).
- Every button features a small descriptive label underneath and glows when active.
- Hovering over a button highlights it.

**Voice & Reading**
- Reads the active file (`audio_sourge.js` or imported file) out loud using the `L` button or key.
- Uses the browser's Italian voice.
- Volume is perfectly matched with the AI voice responses.

**AI Mode**
- `T` — generic chat, with a sarcastic and slightly "Roman" personality.
- `A` — **context-aware** chat: the AI answers based exclusively on the content of `audio_sourge.js` (or the imported file). Neutral and professional tone.
- While waiting for a response, the avatar assumes a "thoughtful" expression and twitches its eyebrows.
- The text input field opens with `Backspace`/`←` or by entering AI mode.

**Import Context (`📁 OPEN FILE`)**
- Click `OPEN FILE` to load a custom `.js`, `.txt`, or `.mjs` file.
- The imported file overrides `audio_sourge.js` for **both** the AI context (`A`) and the Reading (`L`).
- If you click `OPEN FILE` **again** while a file is loaded (button glowing), the context is forgotten, and the avatar reverts to `audio_sourge.js`.

**Break Reminders**
- After 60 minutes of continuous work, it warns you to take a break. Then every 30 minutes, with an increasingly insistent tone.
- Greets you when you return after an absence (standby, screen lock, inactivity).

**Scheduled Messages**
- Fixed-time phrases (lunch break, end of day, etc.) defined in `SCHEDULED_EVENTS`.

---

## Shortcuts (Keyboard)

The UI is in English, but the spoken voice remains in Italian.

| Key | On-Screen Equivalent | Function |
|---|---|---|
| Tap / click | — | Greets, unlocks audio, wakes the avatar (only outside AI mode) |
| `A` | Canvas `A` | AI Mode **with context** |
| `T` | Canvas `T` | AI Mode **without context** |
| `Backspace` / `←` | — | Enters `T` mode and opens the text field (if not already in AI mode). Toggles the field if already in AI mode. |
| `Esc` | — | Closes the text field; if closed, opens the minimal shortcut menu. |
| `L` | Canvas `L` | Reads/interrupts the active file out loud (does not stop AI mode). |
| `S` | Canvas `S` | Subtitles on/off. |
| `M` | Canvas `M` | Total audio mute (starts **unmuted** by default). |
| `N` | Canvas `N` | Reactive microphone on/off (starts **off** by default). |
| `D` | Inside ESC Menu | Shows/hides the microphone debug meter. |

---

## The ESC Menu

Press `Esc` to open a minimal menu containing:
- **Debug Mic (D)**: Toggles the microphone debug overlay.
- **Version**: Displays the current build version (e.g., v1.0.2).
- **GitHub Link**: Direct link to the repository.

---

## Requirements

- **Chrome or Edge** (recommended). Firefox does not support the Web Speech API for voice recognition.
- A **Google Gemini API key** for AI mode.
- A free **Cloudflare** account (for the proxy).
- For the reactive microphone: open the file from `https://` or `http://localhost`. From `file://`, Chrome asks for microphone permission on every reload.

---

## API Architecture (Cloudflare Proxy)

To avoid exposing your API key in the HTML code, the project uses a proxy on Cloudflare Workers.

**How it works:**
1. Your avatar sends the question to your Cloudflare Worker (e.g., `https://cb01-proxy.yourname.workers.dev`).
2. The Worker adds your secret API key (securely stored on Cloudflare) and forwards it to Google (Gemini).
3. The provider processes the response, sends it back to the Worker, which forwards it to your avatar.

### How to set up the Cloudflare proxy

1. **Create an account** at [Cloudflare](https://dash.cloudflare.com).
2. Go to **Workers & Pages** and click **Create application** > **Create Worker**.
3. Name your Worker (e.g., `cb01-proxy`) and click **Deploy**.
4. Click **Edit code**, paste the Worker code, and click **Save and Deploy**.
5. Go to **Settings** > **Variables and Secrets**.
6. Add your **Secret**:
   - **Variable name**: `GEMINI_API_KEY`
   - **Secret value**: your Gemini API key
7. Copy your Worker URL and paste it into `avatar_ai.html` at line:
   ```js
   const PROXY_BASE_URL = 'https://cb01-proxy.yourname.workers.dev';
   ```

---

## Installation (Local or GitHub)

1. **Download the files** (or clone the repo):
   ```text
   git clone https://github.com/cruelben/avatar_ai_cb-01.git
   cd avatar_ai_cb-01
   ```

2. **Configure the avatar**: open `avatar_ai.html` and make sure `PROXY_BASE_URL` points to your Cloudflare Worker.

3. **Open the file** with Chrome or Edge. Tap once to unlock audio. Try out the on-canvas buttons.

---

## How to prepare content for AI Mode (A) and Reading (L)

By default, the avatar reads and uses `audio_sourge.js` as its knowledge base. You can put anything in there: a manual, notes, a technical spec, or text you want to consult by voice.

**Simplest way:** open `audio_sourge.js` with a text editor and write the content between the backticks:
```js
window.AUDIO_SOURGE = `Here goes your text.

An empty line = new paragraph.
`;
```

**Alternative (On-the-fly):** Click the `📁 OPEN FILE` button on the canvas. Select any `.txt` or `.js` file from your computer. The avatar will immediately use this new file for both `A` and `L`. Click the glowing button again to clear the imported file.

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

**Canvas Button Layout.** Search for `BTN_LAYOUT` in the JavaScript to adjust button sizes, margins, and gaps.

---

## Publishing on GitHub Pages

Now that the API keys are safely stored on Cloudflare, **you can publish your avatar on GitHub Pages with total peace of mind**. The HTML file contains no secrets.

**Repository structure:**
```text
avatar_ai_cb-01/
├── avatar_ai.html          # the complete avatar (without API keys)
├── audio_sourge.js         # default text for AI mode and the L key
├── avatar_opzioni_6.html   # configuration editor (optional)
└── README.md               # this file
```

---

## Technical Notes

- **Canvas 2D** for drawing both the avatar and the interactive UI buttons.
- **Web Audio API** for electronic sounds (greeting, yawn).
- **speechSynthesis** for the Italian voice.
- **getUserMedia + AnalyserNode** for the reactive microphone (local volume analysis, no audio recorded).
- **Idle Detection API** (Chromium only) to detect breaks.
- **Gemini API** for AI mode, via Cloudflare proxy.
- **localStorage** to remember preferences (subtitles, microphone), but **Mute is always disabled on reload** to prevent accidental silent starts.

---

## What it does NOT do

- **Does not record** your voice. The reactive microphone only measures volume.
- **Does not spy on you**. Everything runs in your browser and on your personal proxy.
- **Does not replace an assistant**. It is a decorative avatar.

---

## License

MIT — do whatever you want with it, I take no responsibility.