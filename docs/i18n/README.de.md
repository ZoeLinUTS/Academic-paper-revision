# Skill zur Überarbeitung wissenschaftlicher Arbeiten

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | [日本語](README.ja.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Français](README.fr.md) | **Deutsch** | [Português](README.pt.md)
<!-- Übersetzungen werden von der Community gepflegt; Englisch ist maßgeblich und kann voraus sein. -->

---

Ein wiederverwendbarer **Claude-Skill**, der einen rohen oder studentisch anmutenden
Forschungsentwurf (LaTeX) in eine Einreichung verwandelt, die sich wie eine *angenommene* Arbeit
auf einer **bestimmten Ziel-Konferenz** liest. Er ist *konferenzzuerst*: Er fixiert zunächst die
Regeln des Ziels, studiert die angenommenen Arbeiten der Konferenz und schreibt dann um — statt
generische „gute Schreibratschläge" anzuwenden, die für eine bestimmte Konferenz oft falsch sind.

## ✅ Jetzt online ausprobieren — keine Installation, kein API-Key nötig

Eine kostenlose gehostete **Nur-Review**-Version dieses Skills läuft unter
**[zoelin.dev/tools/paper-revision](https://zoelin.dev/tools/paper-revision)**:
PDF oder LaTeX-Quelle hochladen, Ziel-Konferenz wählen, und das Orientierungsbriefing
plus die nach Schweregrad sortierte Befundliste direkt im Browser erhalten — ein
kostenloses gehostetes Modell ist inklusive, oder eigener API-Key für höhere
Ausgabequalität. Die Datei wird nie bearbeitet oder gespeichert; für den Modus
**Vollständige Überarbeitung** (bei dem Claude Ihr `.tex` tatsächlich umschreibt)
nutzen Sie den Skill direkt mit Claude Code, wie unten beschrieben.

## Was in diesem Repository ist

```
SKILL.md                            Der Skill selbst (Anweisungen, denen Claude folgt)
references/venue-conventions.md     Konferenzkonventionen (ACL-Familie, LNCS/AJCAI, ML-Konf.)
docs/USER-GUIDE.md                  Schritt-für-Schritt-Nutzung, die zwei Modi, FAQ
docs/i18n/                          Übersetzte READMEs (Sprachumschalter oben)
examples/student-draft/             Synthetischer zu kurzer Entwurf + .bib + Lösungsschlüssel
examples/over-length/               Synthetischer zu langer Entwurf (Kürzungsübung)
```

## Was der Skill tut

- **Konferenzzuerst-Konventionen.** Vor dem ersten Wort lädt er die Regeln des Ziels
  (Seitenlimit, Pflichtabschnitte, Anonymität, Vorlage). Er weiß z. B., dass in der ACL-Familie
  Limitations/Ethics *kostenlos* sind (nicht zum Limit zählen) und dass LNCS die zukünftige Arbeit
  in die Schlussfolgerung einfaltet.
- **Ton einer angenommenen Arbeit.** Entfernt das RQ/RO-Gerüst zugunsten von Beiträgen in
  Fließtext, nutzt deklarative Substantivphrasen als Überschriften und ordnet verwandte Arbeiten
  thematisch statt chronologisch.
- **Ausgewogene Formalisierung.** Fügt nummerierte Gleichungen und `algorithm`-Floats dort ein, wo
  die Methode zu wenig formalisiert ist, und sichert jede Formel gegen Fehldeutung ab.
- **Struktur- und Querverweis-Korrekturen.** Bindet nicht referenzierte Abbildungen ein, löst
  hängende `\ref`/`\cite` auf und ergänzt fehlende Präambel-Pakete.
- **Seitenanpassung.** Kürzt den Hauptteil auf das Limit durch inhaltserhaltende Bearbeitungen
  (Zusammenlegen von Absätzen, Verschieben eines ausgearbeiteten Beispiels in einen Anhang).
- **Neuheit und Positionierung.** Auf Wunsch rekonstruiert er die Linie der Arbeit, gruppiert
  Vorarbeiten thematisch, benennt das Unterscheidungsmerkmal und gibt eine offene Liste
  wahrscheinlicher Gutachter-Angriffe.

## Zwei Nutzungsweisen

1. **Vollständige Überarbeitung (bearbeitet Ihr LaTeX).** Sie geben Claude die kompilierbare Quelle
   (`.tex` + `.bib` + Klassen-/Stildateien + `figures/` oder ein Overleaf-Export-Zip); er nimmt
   chirurgische Änderungen vor, gleicht hinzugefügte Länge aus und gibt ein neu erstelltes
   Upload-Zip zurück. **Er erfindet niemals Ergebniszahlen.**
2. **Nur-Review (keine Bearbeitung).** Claude liest den Entwurf und liefert *nur Überblick und
   Kommentare*: Forschungsthema/-umfang, Kernbeitrag, offene Neuheitsbewertung, Probleme und
   konkrete Vorschläge — ohne eine Datei zu ändern. Das ist der Modus, den die
   [gehostete Web-App](https://zoelin.dev/tools/paper-revision) bereitstellt.

Beide Modi funktionieren in **jeder Sprache**: Stellen Sie Ihre Fragen und erhalten Sie Überblick,
Kommentare und Zusammenfassungen auf Chinesisch, Spanisch, Arabisch oder jeder anderen Sprache.
Nur das *Gespräch* folgt Ihrer Sprache; das Manuskript bleibt in der von der Konferenz geforderten
Sprache (meist Englisch).

Siehe [`docs/USER-GUIDE.md`](../USER-GUIDE.md) für die ausführliche Anleitung.

## Schnellstart

Nutzung des Skills mit Claude (z. B. Claude Code) in einem Repository, das diesen Skill enthält:

1. Zeigen Sie Claude auf Ihre Arbeitsquelle (oder das Beispiel unten) und nennen Sie Ihre
   **Ziel-Konferenz**.
2. Bitten Sie ihn, *„überarbeite das für <Konferenz>"* (Vollmodus) oder *„lies diesen Entwurf nur
   durch und gib mir einen Überblick und Kommentare, ändere nichts"* (Nur-Review-Modus).
3. Claude gibt zuerst ein **Orientierungsbriefing** und eine **Befundliste** zurück und nimmt dann
   — im Vollmodus — die Änderungen vor und sagt Ihnen genau, was neu zu kompilieren und zu prüfen ist.

Probieren Sie es an den enthaltenen synthetischen Entwürfen aus (beide sind **fiktiv**: erfundene
Autoren, Datensätze und Zahlen, kein geistiges Eigentum; jeweils mit einem Lösungsschlüssel
`EXPECTED-FINDINGS.md`):

```
examples/student-draft/main.tex   unter dem Limit; klassische Probleme studentischer Entwürfe
examples/over-length/main.tex     über dem Seitenlimit; übt das Kürzen
```

- **`student-draft`** ist mit RQ1–RQ3-Gerüst, generischen Überschriften, chronologischem Related
  Work, null Gleichungen und einem `\ref` auf eine Abbildung ohne `\label` bestückt. Es liegt
  *unter* dem Limit und übt so das Hinzufügen kostenloser Abschnitte.
- **`over-length`** zielt auf das ACL-Kurzpapier-Limit (4 Seiten), umfasst aber ~5-6 Seiten; es ist
  ansonsten ausgefeilt und übt so den **Kürzungsschritt** (redundante Absätze zusammenführen, ein
  ausgearbeitetes Beispiel in einen Anhang verschieben).

## Ein Hinweis zu den Eingaben

Der Vollüberarbeitungsmodus benötigt die **LaTeX-Quelle**, kein PDF: Ein PDF lässt sich nicht
chirurgisch bearbeiten, und die Rekonstruktion der Quelle riskiert Formatierung und Zahlen des
Autors. Der Nur-Review-Modus kann aus einem PDF arbeiten, da er nur liest und kommentiert.
