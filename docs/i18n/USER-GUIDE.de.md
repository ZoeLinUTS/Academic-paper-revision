# Benutzerhandbuch

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | [日本語](USER-GUIDE.ja.md) | [한국어](USER-GUIDE.ko.md) | [Español](USER-GUIDE.es.md) | [Français](USER-GUIDE.fr.md) | **Deutsch** | [Português](USER-GUIDE.pt.md)

Wie man den Skill `academic-paper-revision` nutzt, was man ihm gibt, was er zurückgibt und worin sich
die zwei Betriebsmodi unterscheiden.

## 1. Bevor Sie beginnen: was bereithalten

| Modus | Eingabe, die Sie bereitstellen | Was Claude tut |
| --- | --- | --- |
| **Vollständige Überarbeitung** | Die **LaTeX-Quelle**: Haupt-`.tex`, `references.bib`, die Klasse/den Stil der Konferenz (`llncs.cls` + `*.bst` oder `acl.sty`, …) und `figures/`. Ein **Overleaf-Projektexport (zip)** ist ideal. | Nimmt chirurgische Änderungen an der Quelle vor, gleicht hinzugefügte Länge aus und gibt ein neu erstelltes Upload-Zip zurück. |
| **Nur-Review** | Ein **PDF** (oder die Quelle) genügt. | Liest und kommentiert nur — **keine Datei wird geändert**. |

Der Vollmodus braucht die Quelle, weil man ein PDF nicht chirurgisch bearbeiten kann und die
Rekonstruktion der Quelle aus einem PDF Ihre Formatierung und Zahlen zu verlieren riskiert. Ein
gerendertes `main.pdf` ist eine nützliche *sekundäre* Eingabe zum Prüfen von Renderzeit-Eigenschaften
(Seitenzahl, Float-Platzierung), aber allein nie ausreichend.

## 2. Nennen Sie Claude Ihre Ziel-Konferenz

Das ist entscheidend. Dieselbe Änderung kann für eine Konferenz richtig und für eine andere ein
Direkt-Reject sein. Nennen Sie die Konferenz ausdrücklich (ACL, NAACL, EMNLP, AJCAI, NeurIPS, …). Sind
die genauen Regeln der Konferenz unbekannt, schlägt Claude vor dem Bearbeiten den aktuellen
Call-for-Papers nach. Wenn ein Dateiname eine Konferenz sagt, Ihre Notizen aber eine andere, weist er
auf die Diskrepanz hin und fragt nach — er nimmt nie etwas an.

## 3. Wählen Sie einen Modus und fragen Sie

**Vollständige Überarbeitung** — Beispiel-Prompts:
- „Überarbeite das für EMNLP, sodass es sich wie eine angenommene Arbeit liest."
- „Kürze das auf das Seitenlimit von AJCAI und entferne das RQ-Gerüst."
- „Füge eine Gleichung/einen Algorithmus für die Methode hinzu und behebe die Querverweise."

**Nur-Review** — Beispiel-Prompts:
- „Lies diesen Entwurf nur durch — gib mir einen Überblick und Kommentare, ändere nichts."
- „Bewerte die Neuheit und nenne mir die wahrscheinlichen Einwände der Gutachter."
- „Was ist hier der Umfang und die Hauptprobleme? Wie unterscheidet es sich von Vorarbeiten?"

### Kommunikationssprache — jede Sprache

Sie können in **jeder Sprache** arbeiten und Ihre Ergebnisse darin erhalten. Claude fragt (oder
folgert), in welcher Sprache Sie das *Gespräch* wollen — Chinesisch, Spanisch, Arabisch oder jede
andere — und liefert jeden Überblick, Kommentar, Befund und jede Zusammenfassung in dieser Sprache.
Das ist unabhängig vom Manuskript: **die Arbeit bleibt in Englisch** (oder der von der Konferenz
geforderten Sprache); nur das Gespräch folgt Ihrer gewählten Sprache.

## 4. Wie die Ausgabe aussieht

Beide Modi beginnen mit demselben gutachterartigen Memo:

1. **Orientierungsbriefing** — *Was der Entwurf ist* · *Kernbeitrag* · *Neuheit (offen, mit
   Gutachter-Angriffspunkten)* · *Vollständigkeitsstand*. Endet damit, die Konferenz zu bestätigen und
   nach dem gewünschten Umfang zu fragen.
2. **Befundliste** — eine Zeile pro Problem, gruppiert und nach Schweregrad geordnet, jede nennt die
   Korrektur. Beispielzeilen:
   - `[venue]` Ziel = ACL (ARR): 8-seitiger Textkörper, Limitations/Ethics kostenlos, Doppelblind.
   - `[structure]` Explizites RQ1–RQ3-Gerüst → in Fließtextbeiträge umwandeln.
   - `[formalization]` Null Gleichungen, obwohl die Bewertungsregel zentral ist → 2–3 Gl. hinzufügen.
   - `[rigor]` Der Hauptgewinn hat keinen Signifikanztest / einzelner Split → als Hauptschwäche markieren.
   - `[bug]` `\ref{fig:pipeline}` zielt auf eine Abbildung ohne `\label` → das Label hinzufügen.
   - `[cross-ref]` Related Work ist eine chronologische Liste → thematisch neu gruppieren.
   - `[page-fit]` +0,5 Seite zu viel → gepaart mit den genauen `[trim]`-Schritten, die es ausgleichen
     (redundante Absätze zusammenführen, ein ausgearbeitetes Beispiel in einen Anhang verschieben).

**Nur-Review hört hier auf** (plus konkrete Vorschläge und ein ehrlicher Vorbehalt). Keine Datei wird
angefasst.

**Die vollständige Überarbeitung geht weiter:**

3. **Änderungszusammenfassung** (nach dem Bearbeiten) — was sich geändert hat und *warum*, der Reihe
   nach: angewandte Konferenzkonventionen · Rahmung/Struktur · hinzugefügte Formalisierung ·
   Abbildungen · Build-/Querverweis-Korrekturen · Seitenanpassung. Dann **was Sie tun müssen**: neu
   kompilieren (z. B. auf Overleaf), die genannten Renderzeit-Elemente prüfen (Seitenzahl, ein
   bestimmtes Glyph/Float) und den nächsten konkreten Hebel, falls eine Prüfung fehlschlägt. Er
   markiert alles, was er lokal nicht verifizieren konnte, und jede unbestätigte Konferenzregel.
4. **Ein ehrlicher Vorbehalt** — das größte Gutachter-Risiko, klar benannt.

## 4b. Ein ausgearbeitetes Beispiel (Eingabe → Ausgabe)

Konkret sieht ein Lauf über das mitgelieferte `examples/student-draft/` so aus.

**Erwartete Eingabe** — die LaTeX-Quelle (kein PDF) plus eine genannte Konferenz:

```
examples/student-draft/
  main.tex          # article-Klasse-Entwurf: RQ1–RQ3, generische Überschriften,
                    #   null Gleichungen, \ref{fig:pipeline} auf eine Abbildung ohne Label
  references.bib    # bereitgestellt, aber nie mit \cite zitiert
```

> Prompt: *„Prüfe das für **ACL (ARR)** und überarbeite es dann, sodass es sich wie eine angenommene Arbeit liest."*

**Beispielausgabe — das Briefing + die Befundliste, die Claude zuerst zurückgibt:**

```
Orientation briefing
  What it is   : a sentiment classifier that keeps punctuation as tokens; 3 sections.
  Contribution : punctuation-aware tokenization improves accuracy on movie reviews.
  Novelty      : thin as written — the idea is plausible but reads as one ablation.
                 Reviewer attack: "this is a preprocessing tweak, where's the analysis?"
  Completeness : results present but no significance test; one figure renders "??".

Findings (severity-ordered)
  [venue]         Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
  [structure]     Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
  [formalization] Zero equations though the scoring rule is central → add 2–3 eqs.
  [rigor]         Headline gain has no significance test / single split → top weakness.
  [bug]           \ref{fig:pipeline} targets a figure with no \label → add the label.
  [cross-ref]     Related work is a dated list → regroup thematically.
  [venue]         No Limitations section — ACL requires it and it is page-free → add.
```

**Nur-Review** hört dort auf (plus ein paar konkrete Vorschläge und ein ehrlicher Vorbehalt). **Die
vollständige Überarbeitung** nimmt dann die Änderungen vor und gibt eine Änderungszusammenfassung
zurück, die Ihnen genau sagt, was neu zu kompilieren und zu prüfen ist. Bewerten Sie jeden Lauf
gegen das `EXPECTED-FINDINGS.md` des Beispiels.

## 5. Der Overleaf-Hin-und-Rückweg (Vollmodus)

1. In Overleaf: **Menu → Download → Source** (ein zip).
2. Geben Sie Claude das zip (oder die entpackte Quelle).
3. Claude bearbeitet die Quelle und baut ein Upload-Zip neu, das nur die Build-Eingaben (`.tex`,
   `.bib`, Klasse/Stil, `figures/`) enthält — es schließt `main.pdf`, READMEs und Git-Dateien aus.
4. Laden Sie das zip erneut auf Overleaf hoch und kompilieren Sie neu.
5. Prüfen Sie die von Claude genannten Renderzeit-Elemente (die Seitenzahl ist eine
   Renderzeit-Eigenschaft und nur nach dem Kompilieren bestätigbar).

## 6. Garantien zur Forschungsintegrität

- **Erfindet nie Zahlen.** Ergebnis-Platzhalter werden nur aus echten, gespeicherten Läufen gefüllt,
  die Sie bereitstellen. Existieren die Ergebnisse noch nicht, bleiben sie Platzhalter.
- **Bewahrt Ihren fachlichen Inhalt.** Änderungen sind chirurgisch; der intellektuelle Inhalt bleibt,
  nur die Darstellung ändert sich.
- **Keine unaufgeforderten geteilten Aktionen.** Er wird ohne Ihre Aufforderung nichts committen,
  pushen oder senden.

## 7. Probieren Sie es an den Beispielen

Zwei synthetische Entwürfe sind enthalten (fiktive Autoren, Datensätze, Zahlen — kein geistiges
Eigentum), jeder mit einem Lösungsschlüssel `EXPECTED-FINDINGS.md`, gegen den Sie einen Lauf bewerten
können:

- **`examples/student-draft/main.tex`** — *unter* dem Limit, gespickt mit den klassischen Problemen:
  RQ1–RQ3-Gerüst, generische Überschriften („Approach", „Experiment 1"), chronologischer
  Related-Work-Abschnitt, null Gleichungen, obwohl die Bewertungsregel zentral ist, und ein
  `\ref{fig:pipeline}`, dessen Abbildung kein `\label` hat (rendert `??`). Auch kein Signifikanztest —
  der `[rigor]`-Befund.
- **`examples/over-length/main.tex`** — zielt auf das ACL-Kurzpapier-Limit (4 Seiten), umfasst aber
  ~5-6 Seiten. Es ist ansonsten ausgefeilt und übt so den **Kürzungsschritt**: zwei redundante
  Einleitungsabsätze zusammenführen, ein sich wiederholendes Related Work straffen und ein langes
  ausgearbeitetes Beispiel in einen Anhang verschieben.

Führen Sie einen beliebigen Modus auf einem beliebigen Entwurf aus, um das Briefing, die Befundliste
und — im Vollmodus — die Änderungen zu sehen.

## 8. FAQ

**Kann ich nur ein PDF geben?** Für Nur-Review ja. Für die vollständige Überarbeitung nein — fragen
Sie Claude, und er fordert die LaTeX-Quelle an.

**Übersetzt er meine Arbeit in meine Chat-Sprache?** Nein. Das Manuskript bleibt in der Sprache der
Konferenz; nur das Gespräch folgt Ihrer gewählten Sprache.

**Funktioniert er für Nicht-ACL-Konferenzen?** Ja. `references/venue-conventions.md` deckt die
ACL-Familie, Springer/LNCS/AJCAI und ML-Konferenzen ab; für alles andere liest Claude zuerst den
aktuellen CFP und hält fest, was er lernt.

## 9. Nutzung mit anderen KI-Modellen (Codex, DeepSeek, Gemini, …)

Der Skill besteht nur aus **modellunabhängigen Markdown-Anweisungen** — nur der YAML-Frontmatter am
Anfang von `SKILL.md` (der `name:`/`description:`-Block zwischen den `---`-Zeilen) ist
Claude-spezifische Metadaten zum automatischen Laden. Alles darunter ist ein gewöhnlicher Prompt, dem
jedes fähige Modell folgen kann. Zur Nutzung anderswo:

1. **Kopieren Sie den Rumpf von `SKILL.md`** — alles *nach* dem schließenden `---` — in den
   **System-Prompt** des anderen Tools (oder fügen Sie ihn als erste Nachricht ein). Den
   YAML-Frontmatter können Sie weglassen; er zählt nur für Claudes automatische Skill-Erkennung.
2. **Fügen Sie die Referenzdatei** `references/venue-conventions.md` als Kontext bei (einfügen oder in
   das Datei-/Kontextfeld des Tools legen), damit das Modell die Konferenzregeln hat.
3. **Geben Sie ihm die Arbeit auf dieselbe Weise** — die `.tex`-Quelle für die vollständige
   Überarbeitung oder ein PDF für Nur-Review — und nennen Sie die Ziel-Konferenz.

Hinweise je Tool:

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, lokale Modelle:** Fügen Sie den `SKILL.md`-Rumpf als
  System-/Entwickler-Prompt und die venue-conventions-Datei als Kontext ein, dann verfahren Sie wie
  oben. Keine Formatänderungen nötig — alles sind Prosa-Anweisungen.
- **Cursor / Windsurf / Continue und ähnliche IDE-Agenten:** Legen Sie den `SKILL.md`-Rumpf in die
  Projektregeldatei, die das Tool liest (z. B. `.cursorrules`, `AGENTS.md`), damit sie automatisch
  geladen wird, und behalten Sie `references/` im Repository.
- **Was sich unterscheiden kann:** Tool-Aufrufe und Dateibearbeitung laufen über den jeweils eigenen
  Mechanismus des Hosts, daher kann ein Modell ohne Schreibzugriff auf das Repository weiterhin
  **Nur-Review** (nur lesen und kommentieren), braucht aber einen bearbeitungsfähigen Host für die
  **vollständige Überarbeitung**. Die Forschungsintegritätsregel — *niemals Ergebniszahlen erfinden* —
  steht im Prompt selbst, sie überträgt sich also auf jedes Modell, das den Anweisungen folgt.
