# Home Work Location Recognizer

This project explores how a person's likely home and work locations can be inferred from stationary GPS traces. The input data is intentionally simple: each CSV file represents places where one person remained stationary for some amount of time, with latitude, longitude, start time, and duration.

The notebook turns those raw stops into a behavioral map. It parses the location history, computes movement statistics, supports noisy location lookup, and then uses unsupervised spatial clustering to separate likely home areas from likely work areas.

## What The Project Does

The core idea is that home and work are not just coordinates. They are patterns in time and space.

Home is usually where a person spends nights, evenings, and weekend periods. Work is usually where a person spends weekday daytime hours. The notebook uses those assumptions to filter the location history into home-like and work-like windows, then clusters the remaining points by geographic distance.

The result is a set of candidate home and work clusters, including support for people who may have more than one home or work location.

## Data

The repository includes three sample location files:

- `Copy of person.1.csv`
- `Copy of person.2.csv`
- `Copy of person.3.csv`

Each row contains:

- `latitude`
- `longitude`
- `start_time(YYYYMMddHHmmZ)`
- `duration(ms)`

Each file describes stationary locations for one person over time.

## Notebook

The main analysis is in:

- `home_work_location_recognition.ipynb`

It covers four main pieces of work.

### Parsing And Summary Statistics

The notebook reads the semicolon-separated CSV files, assigns meaningful column names, parses timestamps, and calculates basic behavioral statistics such as:

- number of stationary places visited
- median distance traveled between consecutive stationary locations
- time-aware summaries of the movement history

Distances are calculated with the Haversine formula so latitude and longitude are treated as points on the Earth rather than flat Cartesian coordinates.

### Location Lookup

GPS coordinates are noisy. A later reading from the same real-world place may not be exactly equal to an earlier reading. The notebook therefore implements a lookup method that checks whether a queried latitude/longitude pair is close enough to any previously visited location.

Instead of exact matching, it compares the query point against known locations using geographic distance and reports whether the person has likely visited that place before.

### Home And Work Detection

The home/work detector uses a staged unsupervised approach:

1. Split the data into time windows that are likely to represent work and home behavior.
2. Cluster each subset using DBSCAN with the Haversine metric.
3. Remove noisy points that do not belong to dense location clusters.
4. Refine candidate clusters with a smaller radius to isolate tighter location areas.
5. Plot the resulting clusters geographically and in coordinate space.

DBSCAN is a good fit here because it can use geographic distance directly, does not require predefining the number of clusters, and handles outliers naturally.

### Visualization

The notebook includes map and scatter visualizations to make the inferred clusters inspectable. It uses `matplotlib`, `Basemap`, and cluster labels to show how the likely home and work locations are distributed.

## Why This Is Useful

This project is a compact demonstration of mobility-pattern mining. With only stationary GPS points and timestamps, it can infer meaningful places in someone's life without labeled training data.

Potential applications include:

- mobility analytics
- commute pattern analysis
- personalized location services
- geospatial feature engineering
- anomaly detection in movement behavior

Any real-world use of this kind of analysis should be privacy-first. Home and work inference is sensitive, and the safest version of this work is one where users understand and control how their location history is processed.

## Main Techniques

- GPS trace parsing
- Haversine distance
- noisy geospatial lookup
- time-window filtering
- DBSCAN clustering
- outlier-resistant location inference
- geospatial visualization

