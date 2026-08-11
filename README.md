# any-to-md-marker

Hochpräzise Datei-zu-Markdown-Konvertierung — Fork von `any-to-md` mit **marker-pdf** als PDF-Engine.

## Features

- **marker-pdf** für maximale Präzision: OCR (gescannte PDFs), Tabellen, Reading Order, Formeln, Code-Blöcke
- **Drei Engines** wählbar: `marker` | `classic` | `auto`
- **Batch-Modus** für ganze Ordner (rekursiv)
- Fallback: pdfplumber → pymupdf → Tesseract OCR
- Unterstützt auch CSV, XLSX, DOCX, PPTX, HTML, Bilder (OCR) und Audio
  (Whisper, Modell wählbar via `--model`; für Deutsch `small`/`medium` empfohlen)

## Installation

```bash
# Isolierte venv für marker-pdf (WICHTIG: nie in die Haupt-Python-Umgebung installieren!)
cd any-to-md-marker
PYTHONPATH= python -m venv venv
PYTHONPATH= ./venv/Scripts/python -m pip install marker-pdf   # Windows
# PYTHONPATH= ./venv/bin/python -m pip install marker-pdf     # Linux/macOS
```

> **Warnung:** marker-pdf stuft Pillow zurück (10.4.0), was z.B. Hermes-Umgebungen
> bricht (benötigt Pillow==12.3.0). Deshalb **immer die isolierte venv** verwenden.
> **PYTHONPATH muss bei venv-Erstellung/Installation leer sein**, sonst übernimmt
> pip Abhängigkeiten aus der Haupt-venv statt sie sauber in die marker-venv zu
> installieren. Modelle (~2,5 GB) laden beim ersten Lauf nach `~/.cache/huggingface/`.

## Nutzung

```bash
# Einzeldatei
python convert_to_md.py "doku.pdf" "doku.md" --engine marker
python convert_to_md.py "doku.pdf" "doku.md" --engine classic
python convert_to_md.py "doku.pdf" "doku.md" --engine auto

# Batch (ganzer Ordner, rekursiv)
python convert_to_md.py "C:/PDFs" --batch "C:/Markdown" --engine marker
```

## Engines

| Engine | Bedeutung | Wann? |
|---|---|---|
| `marker` | marker-pdf: OCR, Tabellen, Reading Order, Formeln | Gescannt, Tabellen, maximale Präzision |
| `classic` | pdfplumber → pymupdf → Tesseract OCR | Schnell, ohne große Modelle |
| `auto` | marker wenn installiert, sonst classic | Standard |

## Lizenz

MIT
