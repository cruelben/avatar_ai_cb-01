# CB-01 · Desk Avatar

Un avatar neon in stile CRT che vive su un secondo monitor mentre lavori. Respira, sbatte le palpebre, sbadiglia, si addormenta per qualche secondo. Parla con la voce italiana del browser, ti avvisa quando è ora di fare una pausa, reagisce ai rumori del microfono e — con la modalità AI — risponde alle tue domande usando Gemini.

**Demo:** apri `avatar_ai.html` con Chrome o Edge. Fai un tap. Premi `A` o `T`.

---

## Cos'è

CB-01 è un piccolo progetto HTML/JS senza dipendenze, senza build, senza server. Un solo file, un secondo file di testo opzionale (`audio_sourge.js`) che funge da contesto per la modalità AI. Funziona sia da disco (`file://`) sia pubblicato su GitHub Pages.

L'idea è quella di un Tamagotchi da scrivania: non un assistente, non un chatbot, ma una presenza con un po' di carattere che ti tiene compagnia mentre lavori, ti ricorda di staccare e ogni tanto ti prende in giro.

---

## Funzioni principali

**Vita da scrivania**
- Respira, sbatte le palpebre, sbadiglia, si addormenta per pochi secondi, cambia espressione.
- Frasi spontanee ogni tanto ("ho fame", "che palle", "quanto manca?").
- Reagisce ai rumori del microfono: sobbalza ai colpi secchi, si mette in ascolto se parli.

**Voce**
- Legge `audio_sourge.js` ad alta voce con il tasto `L` (utile per promemoria, liste, testi lunghi).
- Voce italiana del browser, sottotitoli opzionali.

**Modalità AI**
- `T` — chat generica con Gemini, con carattere sarcastico e un po' romano.
- `A` — chat **con contesto**: Gemini risponde basandosi solo sul contenuto di `audio_sourge.js`. Tono neutro e professionale.
- Mentre aspetta la risposta, l'avatar assume un'espressione "pensierosa".
- Il campo di scrittura si apre con `Backspace` o con l'ingresso in modalità AI.

**Promemoria pausa**
- Dopo 60 minuti di lavoro continuo ti avvisa di staccare. Poi ogni 30 minuti, con tono sempre più insistente.
- Ti saluta quando torni dopo un'assenza (standby, blocco schermo, inattività).

**Messaggi programmati**
- Frasi a orari fissi (pausa pranzo, fine giornata, ecc.) definite in `SCHEDULED_EVENTS`.

---

## Scorciatoie

| Tasto | Funzione |
|---|---|
| Tap / click | Saluta, sblocca l'audio, sveglia l'avatar |
| `A` | Modalità AI **con contesto** (`audio_sourge.js`) |
| `T` | Modalità AI **senza contesto** (chat generica) |
| `Backspace` | Apre/chiude il campo di scrittura in modalità AI |
| `Esc` | Chiude il campo; se chiuso, apre/chiude il menu scorciatoie |
| `L` | Legge/interrompe `audio_sourge.js` ad alta voce |
| `S` | Sottotitoli on/off |
| `M` | Muto sui suoni elettronici (non sulla voce) |
| `N` | Microfono reattivo on/off |
| `D` | Mostra/nasconde il misuratore del microfono |

---

## Requisiti

- **Chrome o Edge** (consigliati). Firefox non supporta la Web Speech API per il riconoscimento vocale, ma tutte le altre funzioni sì.
- Una **chiave API Google Gemini** per la modalità AI (gratuita, senza carta di credito).
- Per il microfono reattivo: aprire il file da `https://` o da `http://localhost`. Da `file://` Chrome chiede il permesso del microfono a ogni ricarica.
- Connessione internet per la modalità AI e per il riconoscimento vocale (Chrome manda l'audio ai server Google).

---

## Installazione

1. **Scarica i file** (o clona il repo):
   ```
   git clone https://github.com/tuonome/avatar_ai_cb-01.git
   cd avatar_ai_cb-01
   ```

2. **Apri `avatar_ai.html`** con un editor di testo. Cerca la stringa:
   ```js
   const GEMINI_API_KEY = 'INCOLLA_QUI_LA_TUA_CHIAVE';
   ```
   Sostituisci `INCOLLA_QUI_LA_TUA_CHIAVE` con la tua chiave API Gemini, **mantenendo gli apici singoli**.
   La chiave si ottiene gratuitamente da [Google AI Studio](https://aistudio.google.com) → "Get API key".

3. **Apri il file** con Chrome o Edge. Fai un tap per sbloccare l'audio. Fatti un giro con le scorciatoie.

---

## Come preparare il contenuto per la modalità A

La modalità **A** usa il contenuto di `audio_sourge.js` come base di conoscenza. Puoi metterci qualsiasi cosa: un manuale, degli appunti, una specifica tecnica, un testo che vuoi consultare a voce.

**Modo più semplice:** apri `audio_sourge.js` con un editor di testo e scrivi il contenuto tra i backtick:

```js
window.AUDIO_SOURGE = `Qui va il tuo testo.

Una riga vuota = nuovo paragrafo.

Puoi scrivere quanto vuoi, anche molto lungo.`;
```

**Modo consigliato:** usa il tool `avatar_opzioni_6.html`, scheda "📖 Audio Source Editor". Incolla il testo, esporta come `audio_sourge.js`, e sostituisci il file vecchio.

Dopodiché, in modalità A, chiedi a CB-01 cose sul contenuto del file. Lui risponderà solo in base a quello.

---

## Il tool delle opzioni

`avatar_opzioni_6.html` è un editor unificato che ti permette di modificare l'avatar senza toccare il codice a mano. Ha tre schede:

- **📅 Schedule e Frasi** — messaggi programmati agli orari fissi, frasi di risposta ai rumori, frasi di risposta alla voce.
- **🔊 Audio e Reattività** — volumi della voce e dei suoni, soglie del microfono, profili pronti ("Calmo", "Normale", "Sensibile").
- **📖 Audio Source Editor** — il testo di `audio_sourge.js`, con esportazione nel formato corretto.

Apri il tool, carica `avatar_ai.html`, modifica quello che ti serve, premi "💾 GENERA INDEX.HTML" e ottieni un file nuovo con le modifiche. Il tool non tocca la logica dell'avatar, solo i parametri.

---

## Personalizzazione

**Cambiare il carattere della chat (modalità T).** In `avatar_ai.html`, cerca:
```js
const GEMINI_SYSTEM_PROMPT = "Sei CB-01, un avatar da scrivania sarcastico e un po' romano...";
```
Modifica il testo a piacere.

**Cambiare il tono della voce.** In `speakText`, cerca `utterance.rate` e `utterance.pitch`. Valori tipici: `rate = 0.95`, `pitch = 1.05`. Più alto il rate, più veloce; più alto il pitch, più acuto.

**Cambiare i messaggi programmati.** Modifica `SCHEDULED_EVENTS` in `avatar_ai.html`, o usa la scheda "Schedule e Frasi" del tool.

**Cambiare i tempi del promemoria pausa.** Modifica `BREAK_CONFIG` in `avatar_ai.html`. I valori attuali sono: primo avviso dopo 60 minuti, poi ogni 30.

**Cambiare il modello Gemini.** Cerca:
```js
const GEMINI_MODEL = 'gemini-3.5-flash-lite';
```
Se Google depreca il modello, questo è il punto da aggiornare. I nomi attuali li trovi su [AI Studio](https://aistudio.google.com).

---

## Pubblicare su GitHub Pages

Puoi pubblicare l'avatar su GitHub Pages, ma **fai attenzione alla chiave API**.

Un file HTML servito da GitHub Pages è **statico**: qualsiasi cosa ci sia scritta, chiunque può leggerla con "Visualizza sorgente". Quindi, se metti la chiave in chiaro nel file e lo pubblichi, chiunque può copiarla.

**Cosa rischi concretamente:** non hai attivato la fatturazione su Google Cloud, quindi il danno massimo è che qualcuno esaurisca la tua quota gratuita. Il tuo avatar smette di funzionare fino al reset (mezzanotte, ora del Pacifico — in Italia sono le 9 o le 10 del mattino). Poi riparte.

**Come ridurre il rischio (30 secondi):**
1. Vai su [Google Cloud Console](https://console.cloud.google.com) → APIs & Services → Credentials.
2. Trova la tua chiave, clicca "Edit".
3. In "Application restrictions" scegli "HTTP referrers" e aggiungi `tuonome.github.io/*`.
4. In "API restrictions" limita la chiave alla sola "Generative Language API".

Così, anche se qualcuno la trova, non può usarla da un altro sito.

---

## Struttura del repository

```
avatar_ai_cb-01/
├── avatar_ai.html          # l'avatar completo (contiene la chiave API)
├── audio_sourge.js         # il testo usato dalla modalità A e dal tasto L
├── avatar_opzioni_6.html   # editor di configurazione (opzionale, ma comodo)
└── README.md               # questo file
```

---

## Note tecniche

- **Canvas 2D** per il disegno dell'avatar (nessuna libreria).
- **Web Audio API** per i suoni elettronici (saluto, sbadiglio).
- **speechSynthesis** per la voce italiana.
- **getUserMedia + AnalyserNode** per il microfono reattivo (analisi locale del volume, nessun audio registrato o inviato).
- **Idle Detection API** (solo Chromium, con permesso) per rilevare le pause.
- **Gemini API** (`generateContent`) per la modalità AI.
- **localStorage** per ricordare le impostazioni (muto, sottotitoli, microfono).

---

## Cosa NON fa

- **Non registra** la tua voce. Il microfono reattivo misura solo il volume (RMS e picco), non salva né invia nulla.
- **Non ti spia**. Tutto gira nel browser, niente server, niente analytics.
- **Non sostituisce un assistente**. È un avatar decorativo. Non è pensato per rispondere a domande complesse, per gestire codice, o per fare task. È pensato per starti simpatico mentre lavori.

---

## Problemi noti

- **Su `file://`** Chrome chiede il permesso del microfono a ogni ricarica. Se ti dà fastidio, apri il file da un server locale (`python -m http.server`) o pubblicalo su GitHub Pages.
- **Firefox** non supporta `SpeechRecognition`, quindi la voce in input non funziona. Tutto il resto sì.
- **Se due eventi programmati cadono nello stesso minuto**, ne parte solo uno. È un limite noto, non un bug.
- **Il modello Gemini cambia nome spesso.** Se ricevi un errore "model not found", aggiorna `GEMINI_MODEL` con uno dei modelli attivi su AI Studio.

---

## Crediti

Progetto personale, scritto per uso proprio. Se ti piace, prendilo, modificalo, fanne quello che vuoi.

Se trovi un bug o hai un'idea, apri una issue. Non garantisco tempi di risposta, ma leggo tutto.

---

## Licenza

MIT — fai quello che ti pare, non mi assumo nessuna responsabilità.