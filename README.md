# Predicting Competition in Croatian Public Procurement

Capstone project for the General Assembly Data Science course.

## Goal
Predict the **number of bids** a public tender in Croatia will receive, based on what is known before it is published (procedure type, contract type, sector, buyer, estimated value).
Tenders with only one bid mean no real competition, a common red flag in public procurement.

## Data source
**GTI Global Public Procurement Dataset (GPPD) 1/2**, Government Transparency Institute
https://data.mendeley.com/datasets/fwzpywbhgw/3

- Contract-level public procurement data for 42 countries, 2006–2021, collected from official procurement portals and standardised to one format.
- Each record includes buyer and supplier, procedure type, CPV product code, prices and the number of bids received.
- This project uses the Croatia file: `HR_DIB_2023.csv.gz` (one row per bid).
- Licence: CC BY-NC 3.0.

> Fazekas, M., Tóth, B., Abdou, A., & Al-Shaibani, A. (2024). *Global Contract-level Public Procurement Dataset*. Data in Brief, 54, 110412.

The data file is not included in this repo because of its size (~700 MB unzipped). To run the notebooks, download `HR_DIB_2023.csv.gz` from the link above and put it in a `data/` folder.

## Notebooks
| Notebook | What it does |
|---|---|
| `croatia_procurement_eda.ipynb` | Cleaning, one row per tender, EDA of bids by year, procedure, sector, price and buyer |
