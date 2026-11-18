# Data Dictionary – Montenegro Public Procurement Dataset

## 1. Dataset overview

| Item | Description |
|---|---|
| **Source** | Montenegro's electronic public procurement system. Export provided by the system administrator |
| **File** | `Data_science_projekat.xlsx` (4 sheets). The `Tenderi` sheet is also available as `tenderi.csv` |
| **Country** | Montenegro (all contracting authorities) |
| **Time frame** | Tenders published from **2 February 2021** to **16 November 2026** |
| **Currency** | All values in **EUR** |
| **Language** | Column names and text values are in Montenegrin. Some category values are in English |
| **Missing values** | Written as `-` in the raw file |

### Size (after removing exact duplicate rows)

| Sheet | Raw rows | Rows after de-duplication | Columns | One row = |
|---|---|---|---|---|
| `Tenderi` | 329,578 | 164,789 | 35 | one lot in one phase (and one contract, if signed) |
| `Ponude` | 647,010 | 490,095 | 11 | one bid by one bidder for one lot |
| `Odluka o ponistenju` | 5,637 | 5,637 | 3 | one cancellation reason for one tender |
| `Resenja po zalbi` | 1,258 | 1,258 | 3 | one complaint decision type for one tender |

### Key counts

| Entity | Count |
|---|---|
| Tenders (`ID_Tendera`) | 50,596 |
| Lots (`ID_Partije`) | 98,849 |
| Contracting authorities | 581 |
| Unique bidders (by PIB) | 4,060 |
| Tenders with one lot / with several lots | 45,235 / 5,361 |

### Tenders per year

| 2021 | 2022 | 2023 | 2024 | 2025 | 2026* |
|---|---|---|---|---|---|
| 5,548 | 7,412 | 7,475 | 9,494 | 10,882 | 9,785 |

\* 2026 is a partial year (data ends mid-November).

### How the sheets connect

```
Tenderi (ID_Tendera, ID_Partije)
   ├── Ponude               on ID_Tendera + ID_Partije
   ├── Odluka o ponistenju  on ID_Tendera
   └── Resenja po zalbi     on ID_Tendera
```

---

## 2. Sheet `Tenderi` – tenders, lots and contracts

| Column | Type | Description | Example | Missing |
|---|---|---|---|---|
| `ID_Tendera` | int | Unique tender ID | 6005 | 0% |
| `ID_Partije` | int | Unique lot ID (each lot belongs to one tender) | 27221 | 0% |
| `ID_Faze` | int | Phase ID. A lot can go through several phases | 3432 | 0% |
| `Status tendera` | int (code) | Tender status code: 4, 8 or 64* | 8 | 0% |
| `Title` | text | Tender title | Nabavka ljekova i sanitetskog materjala | 0% |
| `Cpvs` | text | CPV codes (type of goods or services), separated by `\|` | \|[24400000] - Ljekovi | 28.2% |
| `Okvirni sporazum` | 0/1 | 1 = framework agreement | 0 | 0% |
| `ID_Narucioca` | int | Contracting authority ID | 569 | 0% |
| `Naziv narucioca` | text | Contracting authority name | UPRAVA ZA IZVRŠENJE KRIVIČNIH SANKCIJA | 0% |
| `PIB` | int | Contracting authority tax ID | 2000458 | 0% |
| `Grad` | text | City of the contracting authority (26 cities, mostly Podgorica) | Podgorica | 0% |
| `Drzava` | text | Country (always Montenegro) | Montenegro | 0% |
| `Tip narucioca` | category | Type of authority: Public service, State body, Sectoral contracting authorities, Local self-government unit, Association | State body | 0% |
| `Vrsta postupka` | category | Procedure type: Open procedure, Small procurement, Negotiation procedure without prior publication, Framework agreement mini call-off, Restricted procedure, Negotiation procedure with prior publication | Open procedure | 0% |
| `Vrsta predmeta` | category | Subject: Goods, Services, Works | Goods | 0% |
| `Faza ukljucuje kriterijume` | 0/1 | 1 = phase uses award criteria | 1 | 0% |
| `Faza ukljucuje cenu` | 0/1 | 1 = phase includes price | 1 | 0% |
| `PublishDatePlaned` | datetime | Planned publication date | 2021-07-09 13:00 | 0% |
| `DateOfOpeningPlaned` | datetime | Planned bid opening date | 2021-07-26 11:30 | 0% |
| `EndOfSubmissionsPlaned` | datetime | Planned deadline for bids | 2021-07-26 11:00 | 0% |
| `Status faze` | int (code) | Phase status code: 1, 4, 8, 32, 64, 512* | 8 | 0% |
| `ID_Ugovora` | int | Contract ID (empty if there is no contract) | 2763 | 51.6% |
| `DateOfConclusion` | datetime | Date the contract was signed** | 2021-10-12 | 51.6% |
| `DateOfExecution` | datetime | Date the contract was carried out | 2022-08-01 | 83.9% |
| `DateOfTermination` | datetime | Date the contract was ended early | 2022-10-25 | 99.6% |
| `Status ugovora` | int (code) | Contract status code: 4, 8, 64* | 64 | 51.6% |
| `Ugovor se potpisuje na procenjenu` | 0/1 | 1 = contract is signed for the estimated value | 0 | 0% |
| `Procenjena vrednost` | float | Estimated value of the lot (EUR). Median 9,020, max ~196M | 145.00 | 0% |
| `Procenjena vrednost (valuta)` | text | Currency (always EUR) | EUR | 0% |
| `Ugovorena vrednost` | float | Contracted value (EUR) | 16530.00 | 88.6% |
| `Broj izmena tenderske` | int | Number of changes to the tender documents | 0 | 0% |
| `Broj podnetih ponuda u fazi` | int | Number of bids received in the phase (0–19) | 2 | 0% |
| `Broj partija u tenderu` | int | Number of lots in the tender (matches the lot count in the data for every tender) | 69 | 0% |
| `Broj zalbi po tenderu` | int | Number of complaints for the tender | 0 | 0% |
| `Broj zahteva za pojasnjenje po tenderu` | int | Number of clarification requests for the tender | 0 | 0% |

\* The meaning of the status codes still needs to be confirmed with the system administrator.
\*\* Some contract dates are in the future (up to 2028), which looks like a data entry error.

---

## 3. Sheet `Ponude` – bids

| Column | Type | Description | Example | Missing |
|---|---|---|---|---|
| `ID_Tendera` | int | Tender ID (links to `Tenderi`) | 23 | 0% |
| `Caption` | category | Bid type in English (same as `Vrsta ponude`) | Independend proposal | 0% |
| `ID_Partije` | int | Lot ID (links to `Tenderi`) | 13 | 0% |
| `Name` | text | Bidder name | BB SOLAR DOO PODGORICA | 0% |
| `PIB` | text | Bidder tax ID. Keep as text so leading zeros stay | 03000168 | 0% |
| `Vrsta ponude` | category | Bid type: Samostalna ponuda (independent), sa podugovaračima (with subcontractors), Zajednička ponuda (joint), Korišćenje sposobnosti drugih subjekata (using other companies' capacities) | Samostalna ponuda | 0% |
| `Ponuda izabrana` | Da/Ne | Whether the bid was chosen* | Da | 0% |
| `Status ugovora` | int (code) | Contract status code: 4, 8, 64 | 64 | 38.8% |
| `Grad` | text | Bidder city (428 cities) | Podgorica | 0% |
| `Drzava` | text | Bidder country (38 countries, mostly Montenegro) | Montenegro | 0% |
| `Ugovorena vrednost` | float | Contracted value for this bidder (EUR) | 81118.22 | 80.8% |

\* "Da" appears more often than "Ne" (300k vs 190k). Its exact meaning still needs to be confirmed.

---

## 4. Sheet `Odluka o ponistenju` – cancellation decisions

| Column | Type | Description | Example |
|---|---|---|---|
| `ID_Tendera` | int | Tender ID (links to `Tenderi`) | 154 |
| `Value` | category | Legal reason for cancellation: Article 140, paragraph 1, point 1–8 of the Public Procurement Law. Most common is point 4 (4,806 cases) | Član 140 stav 1 tačka 4 |
| `Broj Odluka o poništenju postupka javne nabavke` | int | Number of cancellation decisions with this reason | 1 |

---

## 5. Sheet `Resenja po zalbi` – complaint decisions

| Column | Type | Description | Example |
|---|---|---|---|
| `ID_Tendera` | int | Tender ID (links to `Tenderi`) | 154 |
| `Value` | category | Decision on the complaint (16 types). Most common: complaint accepted (615), rejected as unfounded (322), partly accepted (132) | Usvaja se žalba |
| `Broj Rješenja po žalbi` | int | Number of decisions of this type | 1 |

---

## 6. Data quality notes

- **Duplicates:** half of the `Tenderi` rows and about a quarter of the `Ponude` rows are exact duplicates. Remove them before analysis.
- **Rows per lot:** one lot can have several rows in `Tenderi` because of several phases (37,802 lots) or several contracts (16,935 lots).
- **Bids per lot:** the number of different bidders (unique `PIB`) per lot in `Ponude` matches the highest `Broj podnetih ponuda u fazi` for that lot in 99.7% of lots.
- **Lots without bids:** 7,973 lots in `Tenderi` have no rows in `Ponude`, most likely because no bids were received.
- **Unmatched tenders:** 16 tenders in `Ponude` and `Odluka o ponistenju` are not in `Tenderi`.
- **Encoding:** one title contains a hidden control character (shown as `_x0002_` in Excel).
- **Information known only after bidding:** contract fields, complaints and cancellations are only known after the bids come in. Don't use them as inputs when predicting the number of bids.
