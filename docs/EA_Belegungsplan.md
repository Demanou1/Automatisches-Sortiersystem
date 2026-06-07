# E/A-Belegungsplan — Automatisches Sortiersystem
**SPS:** Siemens S7-1500 (CPU 1516-3 PN/DP)  
**Autor:** Ydriss Armel Demanou | 2024

---

## Digitale Eingaenge (DI)

| Adresse | Symbol            | Typ         | Beschreibung                              | Sensor / Geber          |
|---------|-------------------|-------------|-------------------------------------------|-------------------------|
| I0.0    | E_NotHalt         | DI 24V DC   | NOT-HALT Eingang (NC-Kontakt)             | Pilzdrucktaster Schaltschrank |
| I0.1    | E_SensorLicht     | DI 24V DC   | Lichtschranke — Objekt auf Band erkannt   | Sick W4S-3 Lichtschranke |
| I0.2    | E_SensorInduktiv  | DI 24V DC   | Induktivgeber — Metallerkennung           | Balluff BES M08 induktiv |
| I0.3    | E_SensorGross     | DI 24V DC   | Groessensensor — Objekt > 10 cm           | Sick WT4-2 Distanzsensor |
| I0.4    | E_MotorIstlauf    | DI 24V DC   | Rueckmeldung Foerderband-Motor laeuft     | Hilfsschalter Schuetz    |
| I0.5    | E_MotorUeberlast  | DI 24V DC   | Motorschutzschalter ausgeloest (NC)       | Siemens 3RV Motorschutz  |
| I0.6    | E_TuerKontakt     | DI 24V DC   | Schutztuer geschlossen (NC-Kontakt)       | Schutztuer-Sicherheitsschalter |
| I0.7    | RESERVIERT        | —           | Reserviert fuer Erweiterung               | —                        |

---

## Digitale Ausgaenge (DQ)

| Adresse | Symbol            | Typ         | Beschreibung                              | Aktor / Verbraucher      |
|---------|-------------------|-------------|-------------------------------------------|--------------------------|
| Q0.0    | A_Weiche1         | DQ 24V DC   | Weiche 1 ausfahren — Behaelter A          | Pneumatikventil 24V DC   |
| Q0.1    | A_Weiche2         | DQ 24V DC   | Weiche 2 ausfahren — Behaelter C          | Pneumatikventil 24V DC   |
| Q0.2    | A_MotorBand       | DQ 24V DC   | Foerderband-Motor einschalten             | Schuetz Drehstrommotor   |
| Q0.3    | A_LampeGruen      | DQ 24V DC   | Signalleuchte GRUEN — Betrieb OK          | LED-Signalleuchte        |
| Q0.4    | A_LampeRot        | DQ 24V DC   | Signalleuchte ROT — Stoerung aktiv        | LED-Signalleuchte        |
| Q0.5    | A_Hupe            | DQ 24V DC   | Akustischer Alarm bei Stoerung            | Signalhorn 24V DC        |
| Q0.6    | RESERVIERT        | —           | Reserviert fuer Erweiterung               | —                        |
| Q0.7    | RESERVIERT        | —           | Reserviert fuer Erweiterung               | —                        |

---

## Stoerungscode-Tabelle (DB10.iStoerungCode)

| Code | Bedeutung              | Ursache                          | Massnahme                    |
|------|------------------------|----------------------------------|------------------------------|
| 0    | Keine Stoerung         | —                                | —                            |
| 1    | Objektstau             | Objekt > 5s auf Sensor           | Band pruefen, Quittieren     |
| 2    | Sensorfehler           | Sensorsignal inkonsistent        | Sensor pruefen, Quittieren   |
| 3    | Motorueberlast         | Motorschutz ausgeloest           | Motor pruefen, Quittieren    |
| 4    | Schutztuer offen       | Tuer waehrend Betrieb geoeffnet  | Tuer schliessen, Quittieren  |
| 99   | NOT-HALT               | NOT-HALT-Taster gedrueckt        | Ursache beheben, Quittieren  |

---

## Betriebsmodi

| Modus         | Beschreibung                                    | Aktivierung          |
|---------------|-------------------------------------------------|----------------------|
| Automatik     | Vollautomatische Sortierung, Dauerbetrieb       | HMI Taster "AUTO"    |
| Handbetrieb   | Manuelle Steuerung einzelner Aktoren ueber HMI  | HMI Taster "HAND"    |
| Einrichtbetrieb | Schrittweiser Ablauf zur Diagnose             | HMI Taster "EINR"    |
| Stoerung      | Anlage gestoppt, Fehlercode angezeigt           | Automatisch          |
