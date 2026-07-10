# Google Trends Weekly Search Data for Motor Vehicle Theft in U.S. Cities

This repository collects weekly Google Trends data on motor vehicle theft search terms across major U.S. metropolitan areas. The data support research on whether internet search activity can serve as a proxy for crime reporting at the city level.

## Data

The `data/` folder contains the raw scrape batches and the combined outputs:

- **Timestamped batch files** (`<City>_weekly_2017-01-01_2022-12-31_<YYYYMMDD_HH_MM>.csv`): one file per city per pull. Each file holds a weekly time series with a `date` index and a single column `MVT_GT_<City>` of Google Trends relative search volume. Google Trends draws a new sample on every request, so each city was pulled repeatedly (roughly 140 to 170 batches per city) and the batches were later averaged to reduce sampling noise.
- **Per-city aggregates** (`<City>_weekly_total.csv`): the mean of all batches for that city, rescaled so the maximum value equals 100.
- **`all_cities_weekly.csv`**: all per-city aggregate series merged into one table.
- **`crime_counts (Matthew P J Ashby).csv`**: open city-level crime counts used as the ground-truth comparison, from Ashby, M.P.J., "Initial evidence on the relationship between the coronavirus pandemic and crime in the United States," *Crime Science* 9, 6 (2020), https://doi.org/10.1186/s40163-020-00117-6.
- **`sub-est2019_all.csv`**, **`sub-est2021_all.csv`**: U.S. Census Bureau subcounty population estimates.

Coverage: 44 Google Trends metro areas (Nielsen DMA codes; 43 in the United States plus Ontario, Canada), including New York, Los Angeles, Chicago, Houston, Phoenix, Philadelphia, and other populous markets. The search query targets motor vehicle theft reporting: `car stolen+find stolen car+report police stolen car+insurance car stolen-dream-check`.

Each batch combines two overlapping windows, 2017-01-01 to 2021-12-31 and 2018-01-01 to 2022-12-31. The second window is rescaled to the first using the mean ratio of the two series over their overlapping weeks, then the windows are averaged, which places the full 2017 to 2022 period on a common scale.

All Google Trends values are relative search volumes (0 to 100 within a query), not counts of searches.

## Notebooks

- **`GT_Scrapper_Weekly_City.ipynb`**: pulls the weekly series from Google Trends through the `pytrends` API. It loops over the city list and the two date windows, applies the overlap rescaling, writes one timestamped CSV per city per pull, and pauses between requests to respect rate limits. A `torpy` helper is included for rotating the connection when requests are blocked.
- **`Combine_files_and_rescale.ipynb`**: reads all batch files for each city, averages them, normalizes each city series to a maximum of 100, and writes the per-city totals and `all_cities_weekly.csv`. It then merges the search series with the Ashby crime counts for the matched cities and runs exploratory analyses, including interrupted time series models around the March 2020 COVID-19 lockdown.

## Requirements

Python 3 with:

- `pandas`, `numpy`, `scipy`
- `pytrends` (Google Trends API wrapper)
- `torpy` (optional, connection rotation in the scraper)
- `matplotlib`, `seaborn`, `statsmodels`, `scikit-learn`, `graphviz` (analysis sections of the combine notebook)

## How to run

1. Open `GT_Scrapper_Weekly_City.ipynb`, adjust the keyword list, the region code dictionary, and the date windows if needed, then run all cells. Each pull writes a timestamped CSV to the working directory. Expect long run times; the scraper sleeps roughly two minutes between requests.
2. Run `Combine_files_and_rescale.ipynb` from the folder that holds the batch CSVs (now `data/`), or adjust its file paths accordingly. It produces the per-city totals and the combined file, then the merged analysis dataset.

## Repository structure

```
.
├── GT_Scrapper_Weekly_City.ipynb     # Google Trends scraper
├── Combine_files_and_rescale.ipynb   # batch averaging, normalization, analysis
├── data/                             # 7,213 CSV files (batches, totals, crime counts, population)
├── LICENSE
└── README.md
```

## License

MIT License. See `LICENSE`.
