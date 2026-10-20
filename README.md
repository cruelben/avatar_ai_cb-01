# CB-01 · Desk Avatar

Un avatar da scrivania in stile CRT neon che vive sul tuo desktop mentre lavori. Respira, sbatte le palpebre, sbadiglia, si prende brevi pisolini. Parla con la voce italiana del browser, ti ricorda di fare le pause, reagisce ai rumori del microfono e — in modalità AI — risponde alle tue domande usando Gemini o Groq, con o senza un materiale di contesto (contratti, manuali, appunti).

**Versione:** v1.4.0  
**Repository:** [https://github.com/cruelben/avatar_ai_cb-01](https://github.com/cruelben/avatar_ai_cb-01)

---

## Cos'è

CB-01 è un piccolo progetto HTML/JS senza dipendenze, senza build step, senza server. È composto da:

- **`index.html`** — l'avatar completo (canvas, voce, microfono, modalità AI, skin, master volume).
- **`audio_sourge.js`** — file di testo di default, letto dal tasto `L` e usato come contesto dalla modalità `A`.
- **`avatar_opzioni.html`** — editor unificato per modificare configurazione, frasi, volumi, reattività e per convertire PDF/TXT/JS in `audio_sourge.js`.

L'idea è un Tamagotchi da scrivania: non un assistente, non un chatbot, ma una presenza con un po' di personalità che ti tiene compagnia mentre lavori, ti ricorda di staccare e ogni tanto ti prende in giro.

Grazie a un proxy serverless leggero (Cloudflare Workers), l'avatar può usare l'API di Gemini **senza esporre la chiave API nel codice HTML**. Questo permette di pubblicare l'avatar su GitHub Pages senza rischi.

---

## Funzionalità principali

### Vita da scrivania
- Respira, sbatte le palpebre, sbadiglia, dorme per qualche secondo, cambia espressione.
- Frasi spontanee casuali ("ho fame", "che palle", "quanto manca?").
- Reagisce ai rumori del microfono: sobbalza ai colpi secchi, ascolta quando parli.

### Controlli su canvas
Tutte le interazioni principali sono disegnate direttamente sul canvas:
- **Colonna sinistra (centrata):** `A` (AI con contesto), `T` (AI senza contesto), `L` (lettura).
- **Colonna destra (centrata):** `S` (sottotitoli), `M` (mute), `N` (microfono).
- **In alto a destra:** `📁 OPEN FILE` — importa un file di contesto (`.js`, `.txt`, `.mjs`, `.pdf`).
- Ogni bottone ha una piccola etichetta descrittiva sotto e si illumina quando è attivo.
- Passando il mouse sopra un bottone, questo si evidenzia.

### Voce e lettura
- Legge il file attivo (`audio_sourge.js` o file importato) ad alta voce con il tasto `L`.
- Usa la voce italiana del browser.
- Volume perfettamente allineato con le risposte vocali dell'AI.

### Modalità AI
- `T` — chat generica, con personalità sarcastica e un po' romana.
- `A` — **chat con contesto**: l'AI risponde basandosi esclusivamente sul contenuto di `audio_sourge.js` (o del file importato). Tono neutro e professionale.
- Mentre attende una risposta, l'avatar assume un'espressione "pensierosa" e muove le sopracciglia.
- Il campo di testo si apre con `Backspace`/`←` o entrando in modalità AI.

### Import contesto (`📁 OPEN FILE`)
- Clicca `OPEN FILE` per caricare un file personalizzato: **`.js`, `.txt`, `.mjs`, `.pdf`**.
- Il file importato **sovrascrive** `audio_sourge.js` per **entrambe** le modalità: contesto AI (`A`) e lettura (`L`).
- I **PDF** vengono processati in-browser con `pdf.js` (caricato on-demand da CDN). Il testo estratto viene troncato a **200.000 caratteri** con toast di avviso se il PDF è più grande (per evitare di superare i limiti di contesto di Groq).
- Se il PDF è scansionato (immagini, nessun testo estraibile), viene mostrato un avviso chiaro.
- Se clicchi `OPEN FILE` **di nuovo** mentre un file è caricato (bottone illuminato), il contesto viene dimenticato e l'avatar torna a `audio_sourge.js`.

### Master volume (tasto `V`)
- Overlay con slider unico (0–150%) che regola **in tempo reale** i volumi delle tre modalità vocali: **A**, **T**, **L**.
- Muovendo lo slider **mentre l'avatar parla**, la frase in corso **riparte dalla parola corrente** con il nuovo volume (limite tecnico di Web Speech API: non si può cambiare volume a un'utterance già in corso; la ripartenza è la migliore approssimazione possibile).
- Il valore del master viene **persistito in `localStorage`**: al reload resta quello impostato.
- Tasto `R` per reset rapido a 100%.
- Voce `V` anche nel menu `Esc`.

### Promemoria pause
- Dopo 60 minuti di lavoro continuo avvisa di fare una pausa. Poi ogni 30 minuti, con tono sempre più insistente.
- Ti saluta quando torni dopo un'assenza (standby, blocco schermo, inattività).

### Messaggi schedulati
- Frasi a orari fissi (pausa pranzo, fine giornata, ecc.) definite in `SCHEDULED_EVENTS`.

### Skin
- 16 skin disponibili (1 originale wireframe verde + 15 organiche: ameba, medusa, pelosetto, spora, polpetto, germoglio, lucciola, stella, vortice, nuvola, axolotl, goccia, lava, cristallo, camaleonte).
- Selettore con `K` o dal menu `Esc`.

---

## Scorciatoie (tastiera)

L'interfaccia è in inglese, ma la voce parlata resta in italiano.

| Tasto | Equivalente su schermo | Funzione |
|---|---|---|
| Tap / click | — | Saluta, sblocca l'audio, sveglia l'avatar (solo fuori modalità AI) |
| `A` | Canvas `A` | Modalità AI **con contesto** |
| `T` | Canvas `T` | Modalità AI **senza contesto** |
| `Backspace` / `←` | — | Entra in modalità `T` e apre il campo di testo (se non già in AI). Se già in AI, mostra/nasconde il campo. |
| `V` | Canvas (overlay) | Apre/chiude il master volume A/T/L |
| `R` | — | Reset master volume a 100% |
| `Esc` | — | Chiude il campo di testo; se chiuso, apre il menu scorciatoie |
| `L` | Canvas `L` | Legge/interrompe il file attivo ad alta voce (non ferma la modalità AI) |
| `S` | Canvas `S` | Sottotitoli on/off (partono **sempre spenti** all'avvio) |
| `M` | Canvas `M` | Mute audio totale (parte **smutato** di default) |
| `N` | Canvas `N` | Microfono reattivo on/off (parte **spento** di default) |
| `D` | Menu `Esc` | Mostra/nasconde il misuratore di debug del microfono |
| `K` | Menu `Esc` | Apre il selettore skin |
| `G` | Menu `Esc` | Cambia provider AI (Gemini ↔ Groq) |

---

## Il menu `Esc`

Premi `Esc` per aprire un menu minimale con:
- **Skin (`K`)**: apre il selettore skin.
- **Volume (`V`)**: apre il master volume A/T/L.
- **AI Provider (`G`)**: cambia provider (Gemini ↔ Groq).
- **Debug Mic (`D`)**: mostra/nasconde il misuratore del microfono.
- **Versione**: mostra la versione corrente.
- **Link GitHub**: link diretto al repository.

---

## Requisiti

- **Chrome o Edge** (consigliati). Firefox non supporta la Web Speech API per il riconoscimento vocale.
- Una **chiave API di Google Gemini** per la modalità AI (o una chiave Groq, se preferisci).
- Un account **Cloudflare** gratuito (per il proxy).
- Per il microfono reattivo: apri il file da `https://` o `http://localhost`. Da `file://` Chrome chiede il permesso del microfono a ogni reload.

---

## Architettura API (proxy Cloudflare)

Per evitare di esporre la chiave API nel codice HTML, il progetto usa un proxy su Cloudflare Workers.

**Come funziona:**
1. L'avatar invia la domanda al tuo Worker Cloudflare (es. `https://cb01-proxy.tuonome.workers.dev`).
2. Il Worker aggiunge la chiave API segreta (salvata su Cloudflare) e la inoltra a Google (Gemini) o Groq.
3. Il provider elabora la risposta, la restituisce al Worker, che la inoltra all'avatar.

### Come configurare il proxy Cloudflare

1. **Crea un account** su [Cloudflare](https://dash.cloudflare.com).
2. Vai su **Workers & Pages** e clicca **Create application** > **Create Worker**.
3. Dai un nome al Worker (es. `cb01-proxy`) e clicca **Deploy**.
4. Clicca **Edit code**, incolla il codice del Worker e clicca **Save and Deploy**.
5. Vai su **Settings** > **Variables and Secrets**.
6. Aggiungi il tuo **Secret**:
   - **Nome variabile**: `GEMINI_API_KEY` (e/o `GROQ_API_KEY`)
   - **Valore**: la tua chiave API
7. Copia l'URL del Worker e incollalo in `index.html` alla riga:
   ```js
   const PROXY_BASE_URL = 'https://cb01-proxy.tuonome.workers.dev';
   ```

---

## Installazione (locale o GitHub)

1. **Scarica i file** (o clona il repo):
   ```text
   git clone https://github.com/cruelben/avatar_ai_cb-01.git
   cd avatar_ai_cb-01
   ```

2. **Configura l'avatar**: apri `index.html` e assicurati che `PROXY_BASE_URL` punti al tuo Worker Cloudflare.

3. **Apri il file** con Chrome o Edge. Tocca una volta per sbloccare l'audio. Prova i bottoni sul canvas.

---

## Come preparare il contenuto per modalità AI (A) e Lettura (L)

### Caso 1 — Contesto fisso permanente (`audio_sourge.js`)

Se hai un documento che vuoi usare **sempre** come contesto di default (es. il tuo contratto di lavoro, un manuale, un volantino):

1. Apri `avatar_opzioni.html`.
2. Vai nella scheda **📖 AUDIO SOURCE EDITOR**.
3. Clicca `📁 APRI FILE (.TXT, .JS, .PDF)` e seleziona il tuo file.
4. Se è un PDF, il testo viene estratto automaticamente con `pdf.js`.
5. (Opzionale) attiva "Tronca PDF a N caratteri" se il PDF è molto grande.
6. Clicca `💾 ESPORTA COME audio_sourge.js`.
7. Metti il file `audio_sourge.js` nella **stessa cartella** di `index.html`.

Da quel momento in poi, `A` e `L` useranno quel testo automaticamente, senza doverlo importare ogni volta. Funziona anche da `file://` (doppio click sull'HTML).

### Caso 2 — Import temporaneo (`📁 OPEN FILE`)

Se vuoi usare un documento **al volo**, senza convertirlo:

1. Apri `index.html`.
2. Clicca `📁 OPEN FILE` (in alto a destra sul canvas).
3. Seleziona un file **`.js`, `.txt`, `.mjs`, `.pdf`**.
4. Il testo viene estratto (per i PDF, in-browser con `pdf.js`) e diventa il contesto per `A` e `L`.
5. Clicca di nuovo `OPEN FILE` (bottone illuminato) per tornare a `audio_sourge.js`.

### Formati supportati per l'import

| Formato | Contesto A | Lettura L | Note |
|---|---|---|---|
| **`.txt`** | ✅ | ✅ | Testo puro, caso d'uso principale |
| **`.md`** | ✅ | ✅ | Markdown (i simboli vengono letti, sconsigliato per L) |
| **`.js` / `.mjs`** | ✅ | ✅ | Se contiene `window.AUDIO_SOURGE = \`...\``, estrae solo il testo |
| **`.pdf`** | ✅ | ✅ | Estratto in-browser con `pdf.js`. Troncato a 200.000 caratteri in import runtime. PDF scansionati non supportati (servirebbe OCR) |

**Formati non supportati:** `.docx`, `.epub`, `.xlsx`, immagini, audio, video, archivi. Per questi servirebbe una libreria di estrazione dedicata (mammoth.js, epub.js, SheetJS) oppure OCR (`tesseract.js`).

---

## Lo strumento opzioni (`avatar_opzioni.html`)

È un editor unificato che permette di modificare l'avatar senza toccare il codice manualmente. Ha tre schede:

### 📅 Schedule & Phrases
- Eventi schedulati (orari, testo o sequenza, volume normale/sottovoce, attivo/inattivo).
- Frasi di risposta vocale (`VOICE_REPLIES`).
- Reazioni ai rumori secchi (`MIC_PHRASES`).
- Pulsante `▶ Prova` per ascoltare ogni frase con la voce italiana.

### 🔊 Audio & Reactivity
- **Volumi voci A · T · L** (`SPEECH_VOLUME_CONFIG`): i default delle tre modalità vocali, che nell'avatar vengono scalati dal master volume (`V`).
- Altri volumi voce (messaggi schedulati, frasi spontanee, sottovoce, risposte ai rumori).
- Suoni elettronici (saluto, sbadiglio, cinguettio) + interruttore generale.
- Soglie di reattività del microfono (voce prolungata e colpi secchi).
- Profili rapidi: "Solo colpi forti", "Calmo", "Normale", "Sensibile".

### 📖 Audio Source Editor
- Editor del testo che l'avatar legge con `L` (e usa come contesto con `A`).
- **Accetta `.txt`, `.js`, `.mjs`, `.pdf`**.
- Per i PDF: estrazione testo in-browser con `pdf.js` (CDN pinnato).
- **Opzione "Tronca PDF a N caratteri"**: spenta di default (testo integrale), attivabile se vuoi limitare la lunghezza.
- **Avviso chiaro** se il PDF non contiene testo estraibile (scansionato, protetto).
- Contatore caratteri in tempo reale.
- Esporta sempre come `audio_sourge.js` nel formato corretto (`window.AUDIO_SOURGE = \`...\`;`).

### Flusso tipico con l'editor
1. Apri `avatar_opzioni.html`.
2. Clicca `📁 APRI INDEX.HTML` e seleziona il tuo `index.html`.
3. Modifica quello che ti serve nelle tre schede.
4. Clicca `💾 GENERA INDEX.HTML` per salvare l'avatar aggiornato.
5. Se hai modificato la scheda Source, clicca `💾 ESPORTA COME audio_sourge.js` per salvare il file di testo.

---

## Personalizzazione

### Cambiare la personalità della chat (modalità T)
In `index.html`, cerca:
```js
const GEMINI_SYSTEM_PROMPT = "Sei CB-01, un avatar da scrivania sarcastico...";
```

### Cambiare il tono della voce
Cerca `utterance.rate` e `utterance.pitch` dentro `speakText`.

### Cambiare il modello AI
Cerca:
```js
const GEMINI_MODEL = 'gemini-3.5-flash-lite';
const GROQ_MODEL = 'openai/gpt-oss-120b';
```
*(I nomi dei modelli cambiano spesso: se ricevi un errore, aggiorna questo valore).*

### Layout dei bottoni sul canvas
Cerca `BTN_LAYOUT` nel JavaScript per regolare dimensioni, margini e spaziature.

### Volumi base A / T / L
Cerca:
```js
const SPEECH_VOLUME_CONFIG = {
  aiContextVolume: 0.9,
  aiNoContextVolume: 0.9,
  readVolume: 0.85
};
```
Oppure modificali dall'editor `avatar_opzioni.html` (scheda **🔊 Audio**).

### Master volume iniziale
Il master volume è **runtime** e persistito in `localStorage['avatar_master_vol']`. Non è scritto nel file. Se vuoi cambiare il default iniziale, modifica:
```js
let masterVolume = 1.0;
```

---

## Pubblicazione su GitHub Pages

Ora che le chiavi API sono al sicuro su Cloudflare, **puoi pubblicare il tuo avatar su GitHub Pages in totale tranquillità**. Il file HTML non contiene segreti.

**Struttura del repository:**
```text
avatar_ai_cb-01/
├── index.html              # l'avatar completo (senza chiavi API)
├── audio_sourge.js         # testo di default per la modalità AI e il tasto L
├── avatar_opzioni.html     # editor di configurazione (opzionale)
└── README.md               # questo file
```

**Nota importante:** se importi PDF in `index.html` o in `avatar_opzioni.html`, la prima volta il browser scarica `pdf.js` da `cdn.jsdelivr.net` (~1.3 MB). Dopo è in cache. Il testo del PDF non esce mai dal browser.

---

## Note tecniche

- **Canvas 2D** per disegnare sia l'avatar sia i bottoni interattivi dell'interfaccia.
- **Web Audio API** per i suoni elettronici (saluto, sbadiglio).
- **speechSynthesis** per la voce italiana.
- **getUserMedia + AnalyserNode** per il microfono reattivo (analisi locale del volume, nessuna registrazione).
- **Idle Detection API** (solo Chromium) per rilevare le pause.
- **Gemini API / Groq API** per la modalità AI, via proxy Cloudflare.
- **pdf.js** (CDN jsDelivr, pinnato a `4.0.379`) per estrarre testo dai PDF, caricato lazy solo al primo PDF.
- **localStorage** per ricordare:
  - master volume (`avatar_master_vol`, persistito)
  - microfono (`avatar_mic`, persistito)
  - mute (`avatar_muted`, **sempre rimosso all'avvio**: parte smutato)
  - sottotitoli (`avatar_subs`, **sempre rimosso all'avvio**: partono spenti)

---

## Cosa NON fa

- **Non registra** la tua voce. Il microfono reattivo misura solo il volume.
- **Non spia**. Tutto gira nel tuo browser e sul tuo proxy personale.
- **Non sostituisce un assistente**. È un avatar decorativo.
- **Non legge PDF scansionati** (immagini). Per quelli servirebbe un OCR, non supportato.

---

## Risoluzione problemi

### "📖 audio_sourge.js not found"
- Assicurati che `audio_sourge.js` sia nella **stessa cartella** di `index.html`.
- Il nome deve essere esattamente `audio_sourge.js` (tutto minuscolo).

### Il PDF non viene letto
- Verifica che sia un PDF **di testo** (non scansionato). Se apri il PDF e puoi selezionare il testo con il mouse, va bene.
- Se il PDF è enorme (>200k caratteri), viene troncato in import runtime con toast di avviso.

### Il microfono non funziona
- Apri il file da `https://` o `http://localhost`. Da `file://` Chrome chiede il permesso a ogni reload.
- Premi `D` per vedere il misuratore di debug: se il livello è sempre 0, il microfono non è attivo.

### L'AI non risponde
- Verifica che `PROXY_BASE_URL` in `index.html` punti al tuo Worker Cloudflare.
- Verifica che la chiave API sia configurata come Secret sul Worker.
- Apri la console del browser (F12) per vedere l'errore esatto.

### Il master volume non cambia il volume mentre parla
- È un limite tecnico di Web Speech API: non si può cambiare `utterance.volume` a un'utterance già in corso. La ripartenza dalla parola corrente è la migliore approssimazione possibile. Funziona bene su frasi lunghe (>400ms trascorsi, >15 caratteri rimasti).

---

## Licenza

MIT — fai quello che vuoi, non mi assumo nessuna responsabilità.