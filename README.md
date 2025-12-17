# Dashboard AQI – Monitoraggio Qualità dell’Aria

Dashboard web professionale per il monitoraggio della **qualità dell’aria** basata su dati IoT pubblicati su **ThingSpeak**.
Il sistema calcola e visualizza **Indicatori Chiave di Prestazione (KPI)** ambientali e l’**Indice di Qualità dell’Aria (AQI)** secondo gli standard **EPA (USA)** ed **EEA (Unione Europea)**.

---

## Panoramica funzionale

La dashboard consente di:

* visualizzare KPI ambientali in tempo quasi reale
* analizzare l’andamento storico tramite grafici dinamici
* calcolare l’AQI su **media mobile 24 ore**
* confrontare gli standard **EPA** ed **EEA**

Tutti i valori sono derivati da misure reali acquisite tramite sensori e aggregazioni temporali controllate.

---

## Sensori utilizzati

| Parametro   | Sensore | Unità |
| ----------- | ------- | ----- |
| Umidità     | BME680  | %     |
| Temperatura | BME680  | °C    |
| Pressione   | BME680  | hPa   |
| PM1         | PMS5003 | µg/m³ |
| PM2.5       | PMS5003 | µg/m³ |
| PM10        | PMS5003 | µg/m³ |

---

## KPI – Key Performance Indicator Cards

Le **KPI Card** forniscono una sintesi immediata dei principali parametri ambientali:

* Umidità, temperatura e pressione sono mostrati con **1 decimale**
* PM1, PM2.5 e PM10 sono mostrati come **numeri interi**
* PM2.5 e PM10 sono riferiti a **media mobile 24h**

> I valori PM2.5 e PM10 **non vengono visualizzati** finché non sono disponibili almeno **24 ore di dati validi**.

---

## Card Indice di Qualità dell’Aria (AQI)

La card **AQI** fornisce un indicatore sintetico dello **stato della qualità dell’aria delle ultime 24 ore**.

L’utente può selezionare lo standard di riferimento:

* **EPA (USA)**
* **EEA (UE)**

Il risultato è accompagnato da:

* classificazione testuale
* codifica cromatica

---

## Modalità EPA (USA)

**Standard:** US Environmental Protection Agency – Air Quality Index
**Scala:** 0 – 500
**Parametro principale:** PM2.5 (media mobile 24h)

### Metodo di calcolo

Il valore AQI è calcolato tramite **interpolazione lineare tra breakpoint ufficiali EPA**.

```
AQI = (I_hi − I_lo) / (C_hi − C_lo) × (C − C_lo) + I_lo
```

Dove:

* C = concentrazione media 24h misurata
* [C_lo, C_hi] = intervallo di concentrazione EPA
* [I_lo, I_hi] = intervallo AQI associato

Il risultato finale è:

* arrotondato all’intero
* calcolato per PM2.5 e PM10
* **AQI finale = max(AQI_PM2.5, AQI_PM10)**

### Classi AQI EPA (PM2.5 – 24h)

| AQI     | PM2.5 (µg/m³) | Significato                       |
| ------- | ------------- | --------------------------------- |
| 0–50    | 0.0–12.0      | Buona                             |
| 51–100  | 12.1–35.4     | Moderata                          |
| 101–150 | 35.5–55.4     | Non salutare per gruppi sensibili |
| 151–200 | 55.5–150.4    | Non salutare                      |
| 201–300 | 150.5–250.4   | Molto non salutare                |
| 301–500 | ≥ 250.5       | Pericolosa                        |

---

## Modalità EEA (Unione Europea)

**Standard:** European Environment Agency – European Air Quality Index
**Scala:** classi qualitative (1–5)

### Caratteristiche

* **Nessun valore numerico AQI**
* solo **fasce qualitative**
* basate su PM2.5 e PM10 (media 24h)

### Classi PM2.5 (24h)

| Classe | Livello      | PM2.5 (µg/m³) |
| ------ | ------------ | ------------- |
| 1      | Buona        | 0–10          |
| 2      | Discreta     | 10–20         |
| 3      | Moderata     | 20–25         |
| 4      | Scarsa       | 25–50         |
| 5      | Molto scarsa | > 50          |

### Classi PM10 (24h)

| Classe | Livello      | PM10 (µg/m³) |
| ------ | ------------ | ------------ |
| 1      | Buona        | 0–20         |
| 2      | Discreta     | 20–40        |
| 3      | Moderata     | 40–50        |
| 4      | Scarsa       | 50–100       |
| 5      | Molto scarsa | > 100        |

**Regola finale:**

```
Classe AQI EEA = max(Classe_PM2.5, Classe_PM10)
```

---

## Media mobile 24 ore

Le normative internazionali non utilizzano valori istantanei di particolato.

### Definizione matematica

```
PM₂․₅₍₂₄ₕ₎(t) = (1 / N) · Σ PM₂․₅(tᵢ)
```

Dove:

* tᵢ ∈ [t − 24h, t]
* N = numero di campioni validi

La formula è applicata separatamente a PM2.5 e PM10.

---

## Implementazione pratica (ThingSpeak / IoT)

Il sistema di acquisizione opera su più livelli:

* lettura sensori ogni 5–30 secondi
* aggregazione locale continua
* finestra media locale ~30 minuti
* pubblicazione su ThingSpeak ~1 punto / 30 minuti
* ~48 punti per la media mobile 24h

Ogni punto pubblicato rappresenta la media di **decine o centinaia di letture reali**.

---

## Corrispondenza teoria – implementazione

| Aspetto                  | Teoria | Dashboard |
| ------------------------ | ------ | --------- |
| Media mobile 24h         | ✔      | ✔         |
| Breakpoint EPA ufficiali | ✔      | ✔         |
| Interpolazione lineare   | ✔      | ✔         |
| AQI = valore peggiore    | ✔      | ✔         |
| EEA solo fasce           | ✔      | ✔         |
| Blocco prima di 24h      | ✔      | ✔         |

---

## Perché PM1 non influisce sull’AQI

* non normato
* nessun breakpoint ufficiale
* utilizzato solo come indicatore scientifico

---

## Stato del progetto

**Versione:** 6.8 stabile
**Livello:** Produzione / Documentazione scientifica
**Conformità normativa:** EPA – EEA

---

## Licenza

Progetto a scopo scientifico e didattico.
Verificare l’uso dei dati secondo le policy ThingSpeak.
