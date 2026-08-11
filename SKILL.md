---
name: any-to-md-marker
description: "PDF/Dateien hochpräzise zu Markdown (marker-pdf-Engine, OCR)."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [PDF, Markdown, OCR, Conversion, Tables]
    related_skills: [any-to-md, ocr-and-documents, pdf]
---

# any-to-md-marker — Hochpräzise Datei-zu-Markdown-Konvertierung

Fork von `any-to-md` mit **marker-pdf als hochpräziser PDF-Engine**.
Für PDFs, die maximale Präzision erfordern: gescannte Dokumente (OCR),
komplexe Tabellen, Formeln, mehrspaltige Layouts, Code-Blöcke.

## When to Use

- Nutzer möchte PDFs mit **höchster Präzision** in Markdown umwandeln
- Gescannte PDFs / Bild-PDFs, Tabellen, Formeln, komplexe Layouts
- Batch-Konvertierung ganzer Ordner
- "PDF in MD mit OCR", "hochpräzise konvertieren", "marker-pdf"

## Installation (einmalig)

```bash
# 1. Fork-Verzeichnis (falls nicht vorhanden)
#    Quelle: C:/hermes/any-to-md-marker/convert_to_md.py

# 2. Isolierte venv für marker-pdf (WICHTIG: NICHT in Haupt-Python installieren!)
cd C:/hermes/any-to-md-marker
python -m venv venv
./venv/Scripts/python -m pip install marker-pdf   # Windows
# ./venv/bin/python -m pip install marker-pdf     # Linux/macOS
```

> **Warnung:** marker-pdf stuft Pillow auf 10.4.0 zurück. Das bricht die
> Hermes-Umgebung (benötigt Pillow==12.3.0). Deshalb IMMER die isolierte
> venv nutzen — nie `pip install marker-pdf` in die Haupt-venv.
> Modelle (~2,5 GB) werden beim ersten Lauf nach `~/.cache/huggingface/` geladen.

## Engines

| Engine | Bedeutung | Wann? |
|---|---|---|
| `marker` | marker-pdf: OCR, Tabellen, Reading Order, Formeln | Gescannt, Tabellen, maximale Präzision |
| `classic` | pdfplumber → pymupdf → Tesseract OCR | Schnell, ohne große Modelle |
| `auto` | marker wenn installiert, sonst classic | Standard |

## Aufruf

```bash
# Einzeldatei
python C:/hermes/any-to-md-marker/convert_to_md.py "doku.pdf" "doku.md" --engine marker
python C:/hermes/any-to-md-marker/convert_to_md.py "doku.pdf" "doku.md" --engine classic
python C:/hermes/any-to-md-marker/convert_to_md.py "doku.pdf" "doku.md" --engine auto

# Batch (ganzer Ordner, rekursiv)
python C:/hermes/any-to-md-marker/convert_to_md.py "C:/hermes/PDFs" --batch "C:/hermes/Markdown" --engine marker

# Weitere Flags (wie any-to-md)
#   --encoding <enc>   Datei-Encoding für Plaintext
#   --lang <lang>      Tesseract-Sprache, z.B. deu+eng (nur classic)
#   --model <whisper>  Whisper-Modell für Audio (tiny|base|small|medium|large)
```

## Ablauf (Agent)

1. `--engine marker` verwenden, wenn Präzision gewünscht ist oder Scans vorliegen
2. Bei Scans/Tabellen/Formeln IMMER marker — classic liefert dort Rohtext
3. Nach Konvertierung Qualitätscheck: erste ~60 Zeilen der .md lesen
   - Zeilenumbrüche/Tabellen intakt? OCR-Fehler? Encoding?
4. Ergebnis als MEDIA: link präsentieren (absoluter Pfad)

## Hinweise & Fallstricke

- **marker_single findet die venv über `<script_dir>/venv/Scripts/marker_single.exe`**
  — Skript und venv müssen zusammenbleiben
- PYTHONPATH wird beim marker-Aufruf bereinigt (sonst lädt marker Hermes-Pakete)
- Erster marker-Lauf dauert lange (Modell-Download + CPU-Rechenzeit ~1-14 s/Seite)
- Bei GPU: marker nutzt sie automatisch, falls verfügbar
- Wenn marker fehlschlägt → automatischer Fallback auf classic (mit Warnung)
- Andere Formate (CSV, DOCX, PPTX, HTML, Bilder, Audio) nutzen weiterhin
  die bisherigen Konverter aus any-to-md

## Verifikation

Nach Installation prüfen:

```bash
ls C:/hermes/any-to-md-marker/venv/Scripts/marker_single.exe   # Executable vorhanden
python C:/hermes/any-to-md-marker/convert_to_md.py "test.pdf" "test.md" --engine marker
```

Sollte ohne "Fallback classic"-Warnung durchlaufen.
