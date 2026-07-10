# Google Trends Weekly Search Data for Motor Vehicle Theft in U.S. Cities

Weekly Google Trends data on motor vehicle theft search terms for 44 metro areas (43 U.S. DMAs plus Ontario, Canada), 2017-2022, together with the notebooks that collected and combined them.

Google Trends returns a fresh sample on every request, so each area was pulled 140 to 170 times. The timestamped batch files in `data/` keep every pull; `Combine_files_and_rescale.ipynb` averages them into per-city `_weekly_total.csv` series and merges those into `all_cities_weekly.csv`. Each pull stitches two overlapping windows (2017-2021 and 2018-2022) onto one scale using their mean ratio over the overlapping weeks. Values are relative search volume, 0-100 within a query, not search counts.

`GT_Scrapper_Weekly_City.ipynb` did the pulling through pytrends, with long sleeps between requests to stay under the rate limit. The combine notebook also merges the search series with open city-level crime counts from Ashby (2020, *Crime Science*, https://doi.org/10.1186/s40163-020-00117-6), included in `data/` along with Census subcounty population estimates, and runs exploratory interrupted time series around the March 2020 lockdown.

Run the combine notebook from inside `data/`, or adjust its paths, since it expects the batch CSVs in its working directory.
