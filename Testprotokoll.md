# Testprotokoll — Automatisches Sortiersystem
**Autor:** Ydriss Armel Demanou  
**Datum:** Oktober 2024  
**SPS:** Siemens S7-1500 | TIA Portal V17 | PLCSIM Advanced V4.0

---

## 1. Simulationstests (PLCSIM Advanced)

### 1.1 Funktionstests Sortierpfade

| Test | Objekt        | Erwartung       | Ergebnis     | Zykluszeit |
|------|---------------|-----------------|--------------|------------|
| T01  | Metall klein  | Weiche 1 → A    | OK           | 2,3 s      |
| T02  | Metall gross  | Durchlauf → B   | OK           | 2,4 s      |
| T03  | Plastik klein | Durchlauf → B   | OK           | 2,2 s      |
| T04  | Plastik gross | Weiche 2 → C    | OK           | 2,4 s      |

**Ergebnis: 4/4 Sortierpfade korrekt — Fehlerrate 0 %**

### 1.2 Stoerungsszenarien

| Test | Szenario              | Erwartung                    | Ergebnis |
|------|-----------------------|------------------------------|----------|
| S01  | NOT-HALT betaetigen   | Sofortiger Stopp < 50 ms     | OK       |
| S02  | Objektstau (> 5s)     | Stopp + Code 1 am HMI        | OK       |
| S03  | Lichtschranke simulieren | Korrekte Erkennung        | OK       |
| S04  | Quittierung nach Stoerung | Wiederanlauf korrekt     | OK       |

### 1.3 Betriebsmodi

| Test | Modus         | Ergebnis |
|------|---------------|----------|
| B01  | Automatikbetrieb | OK    |
| B02  | Handbetrieb      | OK    |
| B03  | Einrichtbetrieb  | OK    |
| B04  | Moduswechsel waehrend Betrieb | OK |

---

## 2. Gesamtergebnis

| Kennzahl                  | Ziel      | Erreicht   |
|---------------------------|-----------|------------|
| Sortierfehlerrate         | < 2 %     | **< 1 %**  |
| Zykluszeit                | < 3,0 s   | **2,4 s**  |
| NOT-HALT Reaktionszeit    | < 100 ms  | **< 50 ms**|
| Getestete Zyklen gesamt   | 100       | **200**    |
| Kritische Fehler          | 0         | **0**      |
