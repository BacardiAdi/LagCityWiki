---
title: RewardSystem
description: 
published: false
date: 2026-03-24T20:45:51.447Z
tags: 
editor: markdown
dateCreated: 2026-03-24T20:45:51.447Z
---

# RewardSystem – Punkteübersicht & Balancing-Vorschlag

> Erstellt auf Basis der aktuellen Code-Werte in `RewardSystem.cs` und `VoteLogger.cs`.

---

## 1. Spielzeit-Punkte (Ist-Zustand)

### Konfigurationswerte
| Parameter      | Wert   | Beschreibung                                   |
|----------------|--------|------------------------------------------------|
| `PointMinute`  | 0.5    | Punkte pro Minute Spielzeit                    |
| `MaxMinutes`   | 120    | Maximal angerechnete Minuten pro Tag (2h)      |
| `PointDay`     | 10     | Täglicher Login-Bonus (einmalig pro Tag)       |
| `Tick`         | 120s   | Prüfintervall (alle 2 Minuten)                 |

### Faktor-System
Der Faktor startet bei **1.00** am ersten Donnerstag des Monats und steigt um **+0.01 pro Tag**.

| Tag im Zyklus | Faktor | Tages-Punkte (2h) | Tages-Punkte (1h) |
|---------------|--------|--------------------|--------------------|
| Tag 0         | 1.00   | 70.0               | 40.0               |
| Tag 7         | 1.07   | 74.9               | 42.8               |
| Tag 14        | 1.14   | 79.8               | 45.6               |
| Tag 21        | 1.21   | 84.7               | 48.4               |
| Tag 28        | 1.28   | 89.6               | 51.2               |

> **Berechnung pro Tag:** `(PointDay + PointMinute × min(Spielzeit, 120)) × Faktor`  
> Bei 2h: `(10 + 0.5 × 120) × Faktor = 70 × Faktor`  
> Bei 1h: `(10 + 0.5 × 60) × Faktor = 40 × Faktor`

### Monatliche Spielzeit-Punkte

| Szenario                | Punkte/Monat (30 Tage) | Ø Faktor |
|-------------------------|------------------------|----------|
| Jeden Tag 2h spielen    | **~2.405**             | ~1.15    |
| Jeden Tag 1h spielen    | **~1.374**             | ~1.15    |
| 20 Tage × 2h (realist.) | **~1.603**             | ~1.15    |
| 20 Tage × 1h            | **~916**               | ~1.15    |

> *Exakte Berechnung 30 Tage à 2h:*  
> `70 × Σ(d=0..29)(1 + 0.01×d) = 70 × (30 + 0.01 × 435) = 70 × 34.35 = 2.404,5`

---

## 2. Vote-Punkte (Vorschlag)

### Aktuelle VoteLogger-Konfiguration
| Seite          | Punkte/Vote | Max Votes/Tag | Punkte/Tag | Punkte/Monat |
|----------------|-------------|---------------|------------|--------------|
| TopGames       | 5           | 12            | 60         | 1.800        |
| RustServers    | 5           | 1             | 5          | 150          |
| **Gesamt**     |             |               | **65**     | **1.950**    |

> ⚠️ **Problem:** TopGames mit 12 Votes/Tag (60 Pkt) entspricht fast einem ganzen Spieltag – das ist zu viel!

### Empfohlene Vote-Konfiguration

Ziel: Votes sollen **~20-25%** des Spielzeit-Einkommens ausmachen → motivierend, aber kein Ersatz für Spielen.

| Seite                       | Punkte/Vote | Max Votes/Tag | Punkte/Tag | Punkte/Monat |
|-----------------------------|-------------|---------------|------------|--------------|
| **Seite A** (3× täglich)   | 5           | 3             | 15         | 450          |
| **Seite B** (1× täglich)   | 10           | 1             | 10          | 300          |
| **Gesamt**                  |             |               | **25**     | **750**      |

> Seite B gibt mehr pro Vote als Anreiz, trotz nur 1× am Tag.  
> 750 Pkt/Monat ≈ **32%** der Spielzeit-Punkte (bei 2h/Tag) → guter Bonus, kein Ersatz.
---

## 3. Berufsboni (nur bei aktiver Ausübung)

> **Grundregel:** Kein Flat-Bonus. Punkte gibt es **nur pro Dienststunde**, in der der Beruf aktiv ausgeübt wird.  
> **Obergrenze:** Pro Tag werden maximal **3 Dienststunden** vergütet – darüber hinaus gibt es keine zusätzlichen Dienstpunkte.

### Polizei-Bonus (höchster Beruf)

Polizisten leisten aktive RP-Arbeit (Patrouillen, Verhaftungen, Überfälle verhindern).

| Modell           | Wert     | Max Stunden/Tag | Punkte/Tag (max) | Punkte/Monat (20 Diensttage) |
|------------------|----------|-----------------|------------------|------------------------------|
| **Pro Stunde**   | 8 Pkt/h  | 3               | 24               | **480**                      |

> → Bei 2h Dienst: 8 × 2 = **16 Pkt/Tag**  
> → Bei 3h Dienst (Maximum): 8 × 3 = **24 Pkt/Tag** → **480 Pkt/Monat**  
> → Nur wenn aktiv im Dienst (Uniform an, Patrouille, etc.)

### Medic-Bonus

Medics heilen Spieler und leisten Support-RP, aber weniger direkte Konfrontation.

| Modell           | Wert     | Max Stunden/Tag | Punkte/Tag (max) | Punkte/Monat (20 Diensttage) |
|------------------|----------|-----------------|------------------|------------------------------|
| **Pro Stunde**   | 5 Pkt/h  | 3               | 15               | **300**                      |

> → Bei 2h Dienst: 5 × 2 = **10 Pkt/Tag**  
> → Bei 3h Dienst (Maximum): 5 × 3 = **15 Pkt/Tag** → **300 Pkt/Monat**  
> → Nur wenn aktiv im Dienst (Heilungen, Rettungseinsätze, etc.)

### Amt-Bonus (orientiert sich an Medic)

Beamte/Amtsträger übernehmen Verwaltungs- und RP-Aufgaben (Bürgermeister, Richter, etc.).

| Modell           | Wert     | Max Stunden/Tag | Punkte/Tag (max) | Punkte/Monat (20 Diensttage) |
|------------------|----------|-----------------|------------------|------------------------------|
| **Pro Stunde**   | 5 Pkt/h  | 3               | 15               | **300**                      |

> → Bei 2h Dienst: 5 × 2 = **10 Pkt/Tag**  
> → Bei 3h Dienst (Maximum): 5 × 3 = **15 Pkt/Tag** → **300 Pkt/Monat**  
> → Gleiche Stufe wie Medic – Verwaltungsarbeit ist wichtig, aber weniger riskant als Polizei

### Verhältnis der Berufe
| Rolle      | Pkt/Stunde | Max h/Tag | Max Pkt/Tag | Bonus/Monat (20 Tage, max) | Verhältnis |
|------------|------------|-----------|-------------|-----------------------------|-----------:|
| **Polizei**| 8          | 3         | 24          | 480                         |      1.0×  |
| **Medic**  | 5          | 3         | 15          | 300                         |     0.63×  |
| **Amt**    | 5          | 3         | 15          | 300                         |     0.63×  |

> Polizei bekommt ~60% mehr als Medic/Amt – angemessen wegen höherem Risiko und aktiverem RP.  
> Medic und Amt sind gleichwertig – beide leisten wichtige, aber weniger gefährliche Arbeit.  
> **Obergrenze 3h/Tag** verhindert Farming und hält die Dienstpunkte als Bonus, nicht als Haupteinnahme.

---

## 4. Monatliche Aktions-Boni (1× pro Monat)

Diese Boni belohnen besondere RP-Aktionen und sollen Spieler motivieren, diese Inhalte zu nutzen.

| Aktion                    | Punkte | Begründung                                              |
|---------------------------|--------|---------------------------------------------------------|
| **Im Gefängnis sitzen**   | 30     | Passiv, aber RP-fördernd. Belohnung für "gutes Mitspielen" |
| **Tankstellen-Überfall**  | 50     | Aktives RP-Event, mittlerer Aufwand, Risiko Gefängnis   |
| **Banküberfall**          | 100    | Größtes RP-Event, hoher Aufwand, Team nötig, hohes Risiko |

> **Design-Gedanke:** Die Staffelung (30 → 50 → 100) spiegelt den steigenden Aufwand und das Risiko wider.  
> Alle Aktionen zusammen = max **180 Pkt/Monat** (wenn alles gemacht wird).

---

## 5. Gesamtübersicht – Monatliches Einkommen

### Normaler Spieler (20 Tage × 2h, votet täglich)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (20 Tage × 2h)| ~1.603       | 64.3% |
| Vote (30 Tage)           | ~750         | 30.1% |
| Aktionen (alle 3)        | 180          |  7.2% |
| **Beruf: –**             | 0            |  0.0% |
| **Gesamt**               | **~2.533**   |       |

### Aktiver Polizist (20 Tage × 2h Dienst, votet, alle Aktionen)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (20 Tage × 2h)| ~1.603       | 56.2% |
| Vote (30 Tage)           | ~750         | 26.3% |
| Polizei (20×2h à 8/h)   | 320          | 11.2% |
| Aktionen (alle 3)        | 180          |  6.3% |
| **Gesamt**               | **~2.853**   |       |

> *Bei max 3h Dienst/Tag: 480 statt 320 → Gesamt ~3.013*

### Aktiver Medic (20 Tage × 2h Dienst, votet, alle Aktionen)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (20 Tage × 2h)| ~1.603       | 58.6% |
| Vote (30 Tage)           | ~750         | 27.4% |
| Medic (20×2h à 5/h)     | 200          |  7.3% |
| Aktionen (alle 3)        | 180          |  6.6% |
| **Gesamt**               | **~2.733**   |       |

> *Bei max 3h Dienst/Tag: 300 statt 200 → Gesamt ~2.833*

### Aktiver Beamter/Amt (20 Tage × 2h Dienst, votet, alle Aktionen)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (20 Tage × 2h)| ~1.603       | 58.6% |
| Vote (30 Tage)           | ~750         | 27.4% |
| Amt (20×2h à 5/h)       | 200          |  7.3% |
| Aktionen (alle 3)        | 180          |  6.6% |
| **Gesamt**               | **~2.733**   |       |

> *Bei max 3h Dienst/Tag: 300 statt 200 → Gesamt ~2.833*

### Hardcore-Spieler (30 Tage × 2h, votet, alle Aktionen)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (30 Tage × 2h)| ~2.405       | 73.4% |
| Vote (30 Tage)           | ~690         | 21.1% |
| Aktionen (alle 3)        | 180          |  5.5% |
| **Gesamt**               | **~3.275**   |       |

### Gelegenheitsspieler (10 Tage × 1h, votet gelegentlich)

| Quelle                   | Punkte/Monat |   %   |
|--------------------------|--------------|-------|
| Spielzeit (10 Tage × 1h)| ~458         | 63.9% |
| Vote (15 Tage)           | ~345         | 34.2% |
| Aktionen (1 davon)       | 50           |  6.9% |
| **Gesamt**               | **~853**     |       |

---

## 6. Empfohlene VoteLogger-Änderungen

### Seite A (z.B. TopGames – 3 Votes/Tag)
```json
{
  "Amount": 5,
  "MaxRewardsPerDay": 3,
  "Reason": "TopGames Vote Belohnung"
}
```

### Seite B (z.B. RustServers – 1 Vote/Tag)
```json
{
  "Amount": 10,
  "MaxRewardsPerDay": 1,
  "Reason": "RustServers Vote Belohnung"
}
```

---

## 7. Zusammenfassung der empfohlenen Werte

| Parameter                        | Wert      |
|----------------------------------|-----------|
| Spielzeit: PointMinute           | 0.5 ✅    |
| Spielzeit: MaxMinutes            | 120 ✅    |
| Spielzeit: PointDay              | 10 ✅     |
| Vote A: Punkte/Vote             | 5         |
| Vote A: Max/Tag                  | **3** (statt 12) |
| Vote B: Punkte/Vote             | **10** (statt 5) |
| Vote B: Max/Tag                  | 1 ✅      |
| Polizei: Pro Dienststunde        | **8**     |
| Medic: Pro Dienststunde          | **5**     |
| Amt: Pro Dienststunde            | **5**     |
| **Dienst: Max Stunden/Tag**      | **3**     |
| Aktion: Gefängnis (1×/Monat)    | 30        |
| Aktion: Tankstellen-Überfall     | 50        |
| Aktion: Banküberfall             | 100       |

> Die Spielzeit bleibt die Haupteinnahmequelle (~57-64%). Votes sind ein lohnender Bonus (~26-30%).  
> Berufsboni gibt es **ausschließlich pro aktive Dienststunde** – kein Flat-Bonus, **max 3h/Tag**.  
> Polizei (8/h) > Medic = Amt (5/h).

---

## 8. Reward Shop – Artikelkatalog & Preise

> **Referenzwerte** (normaler Spieler, 20 Tage × 2h + Votes):  
> Monatsbudget: **~2.533 Pkt** | Pro Wipe (~1 Monat): **~2.533 Pkt**  
> Ziel: Kein Spieler soll sich *alles* leisten können → bewusste Entscheidungen nötig.

### 8.1 Prefabs & Bau-Dienstleistungen (Ticket)

| Artikel                          | Preis (Pkt) | Limit/Wipe | BuyType  | Hinweis                                          |
|----------------------------------|-------------|------------|----------|--------------------------------------------------|
| **Prefab Klein**                 | 2.000       | –          | ticket   | Kleines Gebäude / Basis                          |
| **Prefab Mittel**                | 3.000       | –          | ticket   | Mittleres Gebäude                                |
| **Prefab Groß**                  | 4.000       | –          | ticket   | Großes / aufwändiges Gebäude                     |
| **Prefab Doppelsession** (2. Mal)| 50% Rabatt  | –          | ticket   | Gleiches Prefab nochmal → halber Preis           |
| **Prefab-Anpassung Klein**       | 1.000       | –          | ticket   | Kleinere Änderungen an bestehendem Prefab        |
| **Prefab-Anpassung Mittel**      | 2.500       | –          | ticket   | Umbauten mittlerer Größe                         |
| **Prefab-Anpassung Groß**        | 5.000       | –          | ticket   | Komplett-Umbau / aufwändige Anpassung            |
| **Ingame-Bauhilfe (30 Min)**     | 500         | –          | ticket   | Admin/Builder baut live mit dir                  |

> Prefabs sind **Premium-Artikel** – ein großes Prefab kostet ~1,6 Monate Spielzeit.  
> Doppelsession-Rabatt belohnt Wiederspieler: 4.000 → 2.000 beim 2. Mal.

### 8.2 Skillpunkte & Attributpunkte (gestaffelt, je 2× pro Wipe)

> **Design:** Preise steigen beim 2. Kauf → bewusste Investition, nicht "jedes Mal kaufen".  
> Beide zusammen (1. Kauf): 1.100 Pkt = ~43% des Monatsbudgets → spürbar, aber machbar.  
> Beide zusammen (1.+2. Kauf): 2.800 Pkt = ~110% des Monatsbudgets → geht nur auf Kosten anderer Items.

| Artikel                      | 1. Kauf (Pkt) | 2. Kauf (Pkt) | Gesamt  | Limit/Wipe | BuyType  |
|------------------------------|---------------|---------------|---------|------------|----------|
| **Skillpunkte-Paket**        | 600           | 900           | 1.500   | 2          | command  |
| **Attributpunkte-Paket**     | 500           | 800           | 1.300   | 2          | command  |

> Skillpunkte kosten mehr als Attribute, da sie direktere Gameplay-Vorteile bieten.  
> Wer beide 2× kauft investiert **2.800 Pkt** → mehr als ein Monatsbudget → klare Priorisierung nötig.

### 8.3 RP-Fahrzeuge (exklusiv)

> **Design:** Fahrzeuge sollen etwas Besonderes sein – sichtbarer Status und Belohnung für treue Spieler.

| Artikel                      | Preis (Pkt) | Limit/Wipe | BuyType  | Hinweis                                   |
|------------------------------|-------------|------------|----------|-------------------------------------------|
| **RP-Fahrzeug Standard**     | 1.000       | 1          | ticket   | Standardmodell, einmal pro Wipe           |
| **RP-Fahrzeug Premium**      | 1.750       | 1          | ticket   | Exklusives Modell, seltener, besser       |

> Standard-Fahrzeug ≈ 2 Wochen normales Spielen → gut erreichbar.  
> Premium-Fahrzeug ≈ 3 Wochen → machbar mit etwas Verzicht auf andere Items.

### 8.4 Custom RP-Items

| Artikel                              | Preis (Pkt) | Limit/Wipe | BuyType  | Hinweis                                           |
|--------------------------------------|-------------|------------|----------|---------------------------------------------------|
| **RP-Item Rezept** (für alle)        | 400         | 1          | command  | Schaltet ein Craft-Rezept frei, jeder kann es dann herstellen |
| **Exklusives Wipe-Item**             | 1.500       | 1          | item     | Einzigartiges Item nur für diesen Wipe, nicht craftbar |

> **RP-Item Rezept (400 Pkt):** Günstig genug, damit viele Spieler es sich leisten → fördert die Wirtschaft.  
> **Exklusives Wipe-Item (1.500 Pkt):** Premium-Feeling – nur wer spart, bekommt es. ~59% des Monatsbudgets.

### 8.5 Ingame-Geld (gestaffelt, je 2× pro Wipe)

> **Design:** Geld-Pakete als Alternative zum Farmen – gestaffelt, damit es nicht zur Haupteinnahme wird.

| Artikel                      | 1. Kauf (Pkt) | 2. Kauf (Pkt) | Gesamt  | Limit/Wipe | BuyType  |
|------------------------------|---------------|---------------|---------|------------|----------|
| **Geld-Paket Klein**         | 250           | 400           | 650     | 2          | item     |
| **Geld-Paket Groß**          | 500           | 750           | 1.250   | 2          | item     |

> Klein ist ein netter Bonus (~10% des Budgets), Groß ist eine bewusste Investition (~20%).  
> Wer beide groß 2× kauft: 1.250 Pkt = ~49% des Monatsbudgets.  
> *Die genauen Geldbeträge (wie viel Ingame-Geld pro Paket) müssen noch festgelegt werden.*

### 8.6 Preisübersicht – Alle Artikel sortiert nach Preis

| #  | Artikel                          | Preis (Pkt)     | Kategorie        |
|----|----------------------------------|-----------------|------------------|
| 1  | Geld-Paket Klein (1.)           | 250             | Geld             |
| 2  | RP-Item Rezept                   | 400             | Custom Items     |
| 3  | Geld-Paket Klein (2.)           | 400             | Geld             |
| 4  | Attributpunkte (1.)             | 500             | Skill/Attribute  |
| 5  | Bauhilfe (30 Min)               | 500             | Bau-Service      |
| 6  | Geld-Paket Groß (1.)            | 500             | Geld             |
| 7  | Skillpunkte (1.)                | 600             | Skill/Attribute  |
| 8  | Geld-Paket Groß (2.)            | 750             | Geld             |
| 9  | Attributpunkte (2.)             | 800             | Skill/Attribute  |
| 10 | Skillpunkte (2.)                | 900             | Skill/Attribute  |
| 11 | RP-Fahrzeug Standard             | 1.000           | Fahrzeuge        |
| 12 | Prefab-Anpassung Klein          | 1.000           | Prefab           |
| 13 | Exklusives Wipe-Item            | 1.500           | Custom Items     |
| 14 | RP-Fahrzeug Premium              | 1.750           | Fahrzeuge        |
| 15 | Prefab Klein                     | 2.000           | Prefab           |
| 16 | Prefab-Anpassung Mittel         | 2.500           | Prefab           |
| 17 | Prefab Mittel                    | 3.000           | Prefab           |
| 18 | Prefab Groß                      | 4.000           | Prefab           |
| 19 | Prefab-Anpassung Groß           | 5.000           | Prefab           |

### 8.7 Beispiel-Budgets – "Was kann ich mir leisten?"

> Basis: Normaler Spieler, 20 Tage × 2h + tägliches Voten = **~2.533 Pkt/Monat**

#### Budget A: "Skill-Fokus" (investiert in Charakterstärke)
| Kauf                        | Pkt   |
|-----------------------------|-------|
| Skillpunkte 1. Kauf         | 600   |
| Attributpunkte 1. Kauf      | 500   |
| RP-Item Rezept              | 400   |
| Geld-Paket Klein 1. Kauf    | 250   |
| **Rest übrig**              | **783** |

#### Budget B: "Fahrzeug-Sparer" (spart auf RP-Fahrzeug)
| Kauf                        | Pkt   |
|-----------------------------|-------|
| RP-Fahrzeug Standard        | 1.000 |
| Geld-Paket Klein 1. Kauf    | 250   |
| **Rest übrig**              | **1.283** |

#### Budget C: "All-In Skills" (maximale Skill-Investition)
| Kauf                        | Pkt   |
|-----------------------------|-------|
| Skillpunkte 1. + 2. Kauf    | 1.500 |
| Attributpunkte 1. Kauf      | 500   |
| **Rest übrig**              | **533** |

#### Budget D: "Sammler" (viele kleine Sachen)
| Kauf                        | Pkt   |
|-----------------------------|-------|
| RP-Item Rezept              | 400   |
| Geld-Paket Klein 1. Kauf    | 250   |
| Geld-Paket Klein 2. Kauf    | 400   |
| Attributpunkte 1. Kauf      | 500   |
| Bauhilfe 30 Min             | 500   |
| **Rest übrig**              | **483** |

> **Ergebnis:** Kein Spieler kann sich alles leisten → bewusste Entscheidungen und unterschiedliche Spielertypen werden gefördert.
