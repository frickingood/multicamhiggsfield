# Multicam

> **Neu bei Claude Code? Lest zuerst die [Team-Anleitung (Deutsch, Schritt für Schritt)](#team-anleitung-für-claude-code-einsteigerinnen) weiter unten.**

Turn ONE talking-head take into a **virtual multi-camera edit** prompt for **Google Omni** — optionally generated end-to-end via the Higgsfield MCP, with your real original audio remuxed back on.

You film a single clip on a single camera. The skill finds the beats of your speech, fills a battle-tested prompt template with four cut timestamps, and Google Omni re-frames your real footage from new camera angles with instant hard cuts — same face, same room, same voice, same lip sync. No second camera, no editing timeline.

## How it works
1. Drop your talking-head video in a folder and ask for the multicam prompt.
2. The skill runs a **preflight check** (`scripts/check_env.py`) and, if anything is missing, explains what it is for and asks permission to install it — so it's plug-and-play.
3. It transcribes your video **word-level** (Whisper, runs locally) to find exactly where each phrase starts and where you breathe.
4. It suggests **4 cut points** on those beats (cuts land on phrase starts, inside breaths, never mid-word) and shows you why.
5. You confirm or adjust the timestamps and the camera angles (left profile, extreme high angle, close-up, and back to the original framing — with presets to swap any of them).
6. It fills the frozen template and saves a ready-to-paste prompt. Upload your clip into Google Omni, select it as the source footage, paste, generate.

## Setup (free and local, no API keys)
The skill checks this for you: `scripts/check_env.py` reports what's missing, what each piece is for, and the install command for your OS — then asks before installing anything. To set it up manually:
- Install **Python 3.8+**.
- Install **ffmpeg** (includes `ffprobe`):
  - macOS: `brew install ffmpeg`
  - Windows: `winget install Gyan.FFmpeg` (or `choco install ffmpeg`)
  - Linux: `sudo apt install ffmpeg`
- Install the Python dependency: `pip install -r requirements.txt`

The first transcription downloads a Whisper model (~460MB for the default `small`) once. Everything else is local: no API key, no account, no paid service. Whisper is multilingual, so it works for any creator's language (the delivered prompt is always English).

## Install the skill
This is a **Claude Code skill**. Clone it into your skills folder:

```bash
git clone https://github.com/frickingood/multicamhiggsfield.git ~/.claude/skills/multicam
```

(Windows: clone into `C:\Users\<you>\.claude\skills\multicam`.)

Then open Claude Code, drop in your clip and say: **"make the multicam prompt for this video"**.

To pull future updates: `cd ~/.claude/skills/multicam && git pull`.

---

## Team-Anleitung für Claude-Code-Einsteiger:innen

Diese Anleitung ist für alle, die noch nie mit Claude Code gearbeitet haben. Sie zeigt Schritt für Schritt, wie ihr aus einem einzigen Talking-Head-Video automatisch mehrere "virtuelle Kamerawinkel" erzeugt — Gesicht, Stimme und Raum bleiben dabei zu 100 % original, es wird nur die Kameraperspektive per KI geschnitten.

### Teil 1: Claude Code einrichten (einmalig)

**1. Zugang zu Claude Code**
Claude Code läuft entweder als Desktop-App (Mac/Windows) oder im Browser unter claude.ai/code. Meldet euch mit eurem Claude-Account an (fragt Franziska bei Bedarf nach einer Einladung). In der Desktop-App: Tab "Code" öffnen.

**2. Ein Terminal ist Pflicht**
Ein paar Setup-Schritte laufen im Terminal (dem Text-Fenster, in dem man Befehle eintippt statt zu klicken). Auf dem Mac: Spotlight öffnen (`Cmd + Leertaste`), "Terminal" eingeben, Enter.

**3. Voraussetzungen installieren**
Der Skill braucht Python, ffmpeg und Whisper (für die Spracherkennung). Mit [Homebrew](https://brew.sh) im Terminal:

```bash
brew install ffmpeg
pip3 install openai-whisper
```

Kein Homebrew? Kein Problem — beim ersten Lauf prüft der Skill das selbst und sagt euch genau, was fehlt und wie ihr es installiert.

**4. Den Workflow-Ordner herunterladen**

```bash
git clone https://github.com/frickingood/multicamhiggsfield.git ~/.claude/skills/multicam
```

Das lädt den Workflow einmalig auf euren Rechner. Ab jetzt ist er in jeder Claude-Code-Session automatisch verfügbar.

### Teil 2: Den Workflow benutzen

1. **Neue Claude-Code-Session starten.**
2. **Video angeben** — zieht eure Video-Datei (`.mp4`/`.mov`) ins Chat-Fenster oder gebt Claude den Dateipfad.
3. **Auslösen** — schreibt z. B. "mach mir den multicam prompt für dieses video" oder den Befehl `/multicam`.
4. **Claude fragt nach, bevor irgendwas fertig ist**: erkannte Sprache/Länge, Vorschlag für die 4 Schnitt-Zeitpunkte (mit Begründung), die 4 Kamerawinkel. Bestätigen oder anpassen lassen.
5. **Ergebnis wählen** — entweder nur den fertigen Prompt (zum manuellen Einfügen in Google Omni), oder direkt automatisch generieren lassen (über Higgsfield, inkl. automatischem Zurücksetzen eurer echten Original-Stimme — kein KI-Voice-Ersatz). Für die automatische Generierung braucht euer Account den Higgsfield-MCP-Connector; meldet euch bei Franziska, falls der noch nicht aktiv ist.
6. **Fertig** — bei der direkten Generierung bekommt ihr am Ende eine fertige `.mp4`-Datei mit dem finalen Multicam-Schnitt und eurer echten Stimme.

### Troubleshooting

| Problem | Lösung |
|---|---|
| `command not found: git` | Terminal öffnen, `xcode-select --install` eingeben und der Anleitung folgen |
| Claude sagt, ein Tool fehlt (Python/ffmpeg/Whisper) | Genau den vorgeschlagenen Befehl ausführen — das ist sicher und Standard |
| Video ist länger als 15 Sekunden | Kein Problem, der Skill deckt trotzdem das ganze Video ab |
| Stimme klingt nach der Generierung komisch | Claude Bescheid geben — der Workflow mixt dann automatisch eure echte Original-Stimme wieder ein |
| Fragen zum Setup | Franziska (franziska.frick@glow25.de) |

## The template
The prompt template is frozen on purpose — its strict preservation rules ("do not alter the face... no changes to audio... only the camera position changes") are what keep your identity, room and voice locked while the virtual camera cuts. The skill only ever fills the four `[Xs]` timestamps and, if you ask, swaps the angle descriptions. Everything else ships exactly as validated in production.

## Files
- `SKILL.md` — the skill (workflow, the frozen template, cut-placement rules, angle presets).
- `scripts/check_env.py` — preflight dependency check (reports what's missing and how to install it).
- `scripts/beats.py` — local word-level transcription (Whisper) + speech-beat report + first-guess cut points.
- `requirements.txt` — the single Python dependency (openai-whisper).

## License
MIT. See `LICENSE`.
