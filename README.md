
# RW – RAG vs. LLM-Wiki

## RAG-System mit n8n, Supabase und OpenAI

### Projektbeschreibung

Entwicklung eines KI-gestützten Wissenssystems auf Basis von **Retrieval-Augmented Generation (RAG)**. Das System ermöglicht die natürlichsprachliche Abfrage interner Unternehmensdokumente mithilfe eines KI-Agenten.

Als Anwendungsbeispiel dient ein fiktiver Elektroinstallationsbetrieb. Der KI-Assistent **RW Bot** beantwortet Fragen zu Baustellen, Installationsmaterialien, Arbeitsleistungen, Projektständen, Nacharbeiten und Abrechnungen auf Grundlage der hinterlegten Dokumente.

## Architektur

Das RAG-System besteht aus zwei getrennten n8n-Workflows.

### 1. Dokumentenverarbeitung und Speicherung

**Google Drive → n8n → PDF-Extraktion → Text Splitter → OpenAI Embeddings → Supabase**

Neue PDF-Dokumente werden über einen Google Drive Trigger erkannt, heruntergeladen und automatisch verarbeitet.

Die extrahierten Texte werden in kleinere Abschnitte aufgeteilt, in numerische Vektorrepräsentationen umgewandelt und zusammen mit den Textinhalten in einer Supabase-Datenbank gespeichert.

**Technische Konfiguration:**

| Komponente | Konfiguration |
|---|---|
| Automatisierung | n8n (Docker, lokal) |
| Datenquelle | Google Drive |
| Dokumentenformat | PDF |
| Text Splitter | Recursive Character Text Splitter |
| Chunk Size | 1.000 Zeichen |
| Chunk Overlap | 100 Zeichen |
| Embedding-Modell | OpenAI `text-embedding-3-small` |
| Vektordimensionen | 1.536 |
| Datenbank | Supabase PostgreSQL mit pgvector |

### 2. KI-Agent und Wissensabfrage

**Chat → AI Agent → Supabase Vector Store → OpenAI Chat Model → Antwort**

Der Nutzer stellt eine Frage über die Chat-Oberfläche von n8n.

Der KI-Agent kann über ein angebundenes Supabase Vector Store Tool auf die gespeicherten Dokumenteninhalte zugreifen.

Die Nutzerfrage wird mithilfe desselben Embedding-Modells vektorisiert. Anschließend werden über die PostgreSQL-Funktion `match_documents` semantisch passende Textabschnitte ermittelt.

Diese Informationen dienen dem Sprachmodell als Kontext für die Antwortgenerierung.

**Technische Konfiguration:**

- n8n AI Agent mit OpenAI Chat Model
- Supabase Vector Store als Agenten-Tool
- Semantische Suche mittels pgvector und Cosine Similarity
- Retrieval-Limit: 4 Dokumentenabschnitte
- PostgreSQL Chat Memory zur Speicherung des Gesprächsverlaufs
- Systemanweisungen zur bevorzugten Verwendung interner Projektdaten

## Testdaten

Für die Entwicklung wird eine fiktive Baustellendokumentation mit drei Elektroinstallationsprojekten verwendet.

Die Dokumentation enthält unter anderem:

- Leitungstypen, Kabellängen und Materialpreise
- Steckdosen, Schalter und Elektroverteilungen
- Arbeitsstunden und Installationskosten
- Baustellenstatus und technische Freigaben
- Mängel, Nacharbeiten und offene Leistungen
- Auftragswerte, Zahlungen und Nachträge

Sämtliche Kunden- und Geschäftsdaten sind fiktiv.

## Aktueller Entwicklungsstand

- [x] n8n lokal über Docker eingerichtet
- [x] Google Drive als Dokumentenquelle angebunden
- [x] Automatisierte PDF-Verarbeitung eingerichtet
- [x] Chunking und Embedding-Erstellung implementiert
- [x] Supabase-Vektordatenbank eingerichtet
- [x] Dokumente erfolgreich in Supabase gespeichert
- [x] AI Agent mit Supabase Vector Store verbunden
- [x] PostgreSQL Chat Memory konfiguriert
- [ ] Systematische Prüfung der Antwortqualität

---

**Status:** RAG-Prototyp implementiert, Funktionstests und Evaluation in Bearbeitung.
