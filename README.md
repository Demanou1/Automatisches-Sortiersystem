# Automatisches Sortiersystem mit Siemens S7-1500 (TIA Portal)

![Status](https://img.shields.io/badge/Status-Abgeschlossen%202024-brightgreen)
![TIA Portal](https://img.shields.io/badge/TIA%20Portal-V17-blue)
![Siemens](https://img.shields.io/badge/SPS-S7--1500-blue)
![Sprache](https://img.shields.io/badge/Sprache-SCL%20%7C%20Ladder-orange)
![Simulation](https://img.shields.io/badge/Simulation-PLCSIM-lightgrey)

> **SPS-gesteuerte Förderband-Anlage mit sensorbasierter Objekterkennung und automatischer Sortierung nach Typ und Größe — programmiert in SCL und Ladder auf einer Siemens S7-1500.**

---

## Inhaltsverzeichnis

- [Projektübersicht](#projektübersicht)
- [Systemarchitektur](#systemarchitektur)
- [Technologien & Hardware](#technologien--hardware)
- [Funktionsbeschreibung](#funktionsbeschreibung)
- [Programmstruktur](#programmstruktur)
- [Ergebnisse & Kennzahlen](#ergebnisse--kennzahlen)
- [Simulation & Test](#simulation--test)
- [Projektstruktur](#projektstruktur)
- [Installation & Nutzung](#installation--nutzung)
- [Autor](#autor)

---

## Projektübersicht

Dieses Projekt entstand im Rahmen des Masterstudiums Elektrotechnik an der Westfälischen Hochschule Gelsenkirchen. Ziel war die Entwicklung einer vollautomatischen Sortieranlage, die Objekte auf einem Förderband anhand von **Typ und Größe** erkennt und automatisch in die jeweiligen Behälter umleitet.

Das System wurde vollständig in **TIA Portal V17** programmiert, mit **PLCSIM Advanced** simuliert und anschließend auf einer realen **Siemens S7-1500** getestet.

### Ziele
- Automatische Objekterkennung über Lichtschranken und induktive Sensoren
- Sortierung nach Material (metallisch / nicht-metallisch) und Größe (klein / groß)
- Robuste Ablaufsteuerung mit Fehlerbehandlung und Quittierung
- Zykluszeit unter 2,5 Sekunden bei Sortierfehlerrate < 1 %

---

## Systemarchitektur

```
┌─────────────────────────────────────────────────────────┐
│                    Siemens S7-1500                       │
│                  (CPU 1516-3 PN/DP)                     │
│                                                          │
│   ┌──────────┐    ┌──────────┐    ┌──────────────────┐  │
│   │   OB1    │    │  FB10    │    │      DB10        │  │
│   │ (Main)   │───▶│Sortierung│───▶│ Prozessdaten     │  │
│   └──────────┘    └──────────┘    └──────────────────┘  │
│         │                                                │
│   ┌──────────┐    ┌──────────┐                          │
│   │  FB20    │    │  FC10    │                          │
│   │Förderband│    │Sensoraus-│                          │
│   │-Steuerung│    │wertung   │                          │
│   └──────────┘    └──────────┘                          │
└───────────┬─────────────────────────────────────────────┘
            │ PROFINET
    ┌────────▼────────────────────────────────────┐
    │              Feldebene                       │
    │                                              │
    │  [Lichtschranke]  [Induktiv-Sensor]         │
    │  [Weiche 1]       [Weiche 2]                │
    │  [Motor Förderband]  [HMI-Panel]            │
    └──────────────────────────────────────────────┘
```

---

## Technologien & Hardware

| Kategorie | Komponente | Details |
|---|---|---|
| **SPS** | Siemens S7-1500 | CPU 1516-3 PN/DP |
| **Software** | TIA Portal | Version V17 |
| **Programmiersprache** | SCL + Ladder (KOP) | Ablaufsteuerung in SCL, E/A in KOP |
| **Simulation** | PLCSIM Advanced | V4.0 — vollständige Softwaresimulation |
| **Sensorik** | Lichtschranken | Objekterkennung auf dem Band |
| **Sensorik** | Induktive Sensoren | Metallerkennung (metallisch / nicht-metallisch) |
| **Aktorik** | 2× pneumatische Weichen | Umleitung in Behälter A / B / C |
| **Aktorik** | Drehstrommotor | Antrieb des Förderbands |
| **Visualisierung** | Siemens HMI TP700 | Betriebszustand, Fehlermeldungen, Quittierung |

---

## Funktionsbeschreibung

### Ablauf (Sequenzdiagramm)

```
Objekt erkannt (Lichtschranke)
        │
        ▼
Größenmessung (Breite via Sensor-Timing)
        │
        ├──── groß ──────────────────────┐
        │                               │
        ▼                               ▼
Metalldetektion (Induktiv)       Metalldetektion (Induktiv)
        │                               │
   ┌────┴────┐                     ┌────┴────┐
   │Metall   │Kein Metall          │Metall   │Kein Metall
   ▼         ▼                     ▼         ▼
Behälter A  Behälter B          Behälter B  Behälter C
(Metall kl.)(Plastik kl.)      (Metall gr.) (Plastik gr.)
```

### Betriebsmodi

| Modus | Beschreibung |
|---|---|
| **Automatikbetrieb** | Vollautomatische Sortierung, Dauerbetrieb |
| **Handbetrieb** | Manuelle Steuerung einzelner Aktoren über HMI |
| **Einrichtbetrieb** | Schrittweiser Ablauf zur Diagnose und Justierung |
| **Störungsbetrieb** | Anlage stoppt, Fehler wird am HMI angezeigt, Quittierung erforderlich |

### Fehlerbehandlung

- Bandstillstand bei Sensorausfall → automatische Meldung am HMI
- Objektstau-Erkennung über Zeitüberwachung (TON-Timer)
- Quittierungslogik mit Störungsprotokoll im Datenbaustein DB10
- NOT-HALT Integration gemäß EN 60204-1

---

## Programmstruktur

```
Sortiersystem_S7-1500/
│
├── OB1_Main.scl              ← Hauptorganisationsbaustein
├── FB10_Sortierlogik.scl     ← Kernlogik der Sortierung (SCL)
├── FB20_Foerderband.scl      ← Förderband-Steuerung (SCL)
├── FC10_Sensorauswertung.scl ← Sensor-Signalverarbeitung
│
├── DB10_Prozessdaten.db      ← Globaler Datenbaustein
├── DB20_Stoerungslog.db      ← Störungsprotokoll
│
├── HMI/
│   ├── Hauptbild.png         ← HMI-Bildschirm Hauptansicht
│   └── Stoerungsbild.png     ← HMI-Bildschirm Störungen
│
├── Dokumentation/
│   ├── Schaltplan.pdf        ← E/A-Belegung und Verdrahtung
│   ├── Funktionsbeschreibung.pdf
│   └── Testprotokoll.pdf     ← Ergebnisse der Inbetriebnahme
│
└── Simulation/
    └── PLCSIM_Konfiguration/ ← Simulationskonfiguration
```

### Wichtigste Bausteine

#### `FB10_Sortierlogik` (SCL)
Implementiert die Zustandsmaschine der Sortierung:
- **Zustand 0** – Warten auf Objekt
- **Zustand 1** – Größenmessung aktiv
- **Zustand 2** – Metalldetektion
- **Zustand 3** – Weichensteuerung
- **Zustand 4** – Ablaufende / Reset

#### `FB20_Foerderband` (SCL)
- Drehzahlsteuerung über Frequenzumrichter-Schnittstelle
- Überwachung Motorstrom (Überlastschutz)
- Anlauf- und Bremsrampen

#### `FC10_Sensorauswertung` (KOP)
- Entprellung der digitalen Eingänge (10 ms)
- Signalqualifizierung Induktivgeber
- Impulslängen-Messung zur Größenbestimmung

---

## Ergebnisse & Kennzahlen

| Kennzahl | Zielwert | Erreichter Wert |
|---|---|---|
| Zykluszeit (ein Objekt) | < 3,0 s | **2,4 s** ✓ |
| Sortierfehlerrate | < 2 % | **< 1 %** ✓ |
| Reaktionszeit Sensor → Weiche | < 200 ms | **~150 ms** ✓ |
| Durchsatz | > 20 Obj./min | **25 Obj./min** ✓ |
| NOT-HALT Reaktionszeit | < 100 ms | **< 50 ms** ✓ |

---

## Simulation & Test

### Simulationsvorgehen

Das System wurde vor der realen Inbetriebnahme vollständig mit **PLCSIM Advanced V4.0** simuliert:

1. Virtuelles E/A-Abbild der Sensoren per Skript angesteuert
2. Alle 4 Sortierpfade (A, B, C + Fehler) durchgetestet
3. Störungsszenarien simuliert: Sensorausfall, Objektstau, NOT-HALT
4. Zykluszeiten gemessen und Ablauflogik optimiert

### Testergebnisse

- **200 Testzyklen** durchgeführt (Simulation + real)
- **0 kritische Fehler** nach finaler Programmoptimierung
- Alle Betriebsmodi (Auto / Hand / Einricht / Störung) validiert

---

## Projektstruktur (Repository)

```
📁 Automatisches-Sortiersystem/
├── 📄 README.md
├── 📁 src/                  ← TIA Portal Quellcode (.scl, .xml)
├── 📁 docs/                 ← Schaltpläne, Funktionsbeschreibung
├── 📁 hmi/                  ← HMI-Screenshots
├── 📁 simulation/           ← PLCSIM-Konfiguration
└── 📁 media/                ← Fotos, Demo-Video (GIF)
```

---

## Installation & Nutzung

### Voraussetzungen

- Siemens TIA Portal V17 oder höher
- PLCSIM Advanced V4.0 (für Simulation ohne Hardware)
- Siemens S7-1500 CPU (für realen Betrieb)

### Projekt öffnen

```bash
# Repository klonen
git clone https://github.com/Demanou1/Automatisches-Sortiersystem.git

# TIA Portal öffnen
# → Projekt → Öffnen → Sortiersystem_S7-1500.ap17
```

### Simulation starten

```
1. PLCSIM Advanced V4.0 starten
2. Neue Instanz erstellen: "Sortiersystem_Sim"
3. TIA Portal → SPS → Laden in Gerät (PLCSIM)
4. SPS in RUN schalten
5. Simulation über HMI oder Watch-Table bedienen
```

---

## Autor

**Ydriss Armel Demanou**
M.Eng. Elektrotechnik — Automatisierung & Energiesysteme
Westfälische Hochschule Gelsenkirchen

- Email: demanouarmel@yahoo.com
- LinkedIn: [linkedin.com/in/ydriss-armel-demanou](https://www.linkedin.com/in/ydriss-armel-demanou-4)
- GitHub: [github.com/Demanou1](https://github.com/Demanou1)

---

*Dieses Projekt entstand im Rahmen des Masterstudiums Elektrotechnik an der Westfälischen Hochschule Gelsenkirchen (2024).*
