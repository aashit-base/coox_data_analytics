# COOX Geospatial Pincode Blocking Strategy

This repository contains the code, dataset, and interactive visualizations required to analyze COOX's non-serviceable (refunded) bookings. It includes the complete pipeline to clean the data, perform density-based geospatial clustering (DBSCAN), and reverse-geocode coordinates to generate a hyper-targeted pincode blocklist.

---

## Repository Structure

```text
COOX_GEO_BLOCKING/
│
├── graphs_maps/                            # Exported visualizations and boundaries
│   ├── address_type_distribution.png
│   ├── city_wise_bookings.png
│   ├── cluster_map.html
│   ├── geo_boundaries.html
│   ├── heatmap.html
│   ├── k_distance_elbow.png
│   ├── state_wise_bookings.png
│   └── top_problem_areas.png
│
├── data_analysis.ipynb                     # Main Jupyter Notebook with all logic
├── Dataset_Coox.csv                        # The raw refunded bookings dataset
├── final_blocked_pincodes.csv              # Final deduplicated delivery blocklist
├── pincode_blocklist.csv                   # Raw extraction before deduplication
├── problem_statement.pdf                   # Original project brief
├── Project_Report.pdf                      # Final formal report detailing findings
├── requirements.txt
└── README.md
```

---

## Setup Instructions

**1. Create and activate a virtual environment** *(optional but recommended)*

```bash
python -m venv venv
source venv/bin/activate        # Linux / Mac
venv\Scripts\activate           # Windows
```

**2. Install required dependencies**

```bash
pip install -r requirements.txt
```

> Tested with Python 3.x. Core libraries include `pandas`, `scikit-learn`, `folium`, `shapely`, and `geopy`.

---

## How to Run the Code

All project logic is contained within `data_analysis.ipynb`. Open the notebook and run the cells sequentially.

### 1. Data Preprocessing & EDA
Cleans `Dataset_Coox.csv`, removes null coordinates, and generates the initial baseline visualizations (saved to the `graphs_maps/` folder).

### 2. Geospatial Clustering (DBSCAN)
Applies DBSCAN with a strictly tuned **2 km radius** and a minimum sample size of **3**. This filters out statistical noise (one-off failed deliveries) and identifies **53 distinct neighborhood-level hotspots**.

### 3. Boundary Generation
Uses Shapely to draw Convex Hull polygons around the clusters, defining literal "No-Go" zones. These are exported as interactive Folium maps (`geo_boundaries.html` and `cluster_map.html`).

### 4. Pincode Extraction
Uses the Nominatim API to reverse-geocode the centroid of each hotspot.

> **Note:** The extraction loop contains a `time.sleep(1)` command to respect open-source API rate limits. Let it run completely. This generates `pincode_blocklist.csv`.

### 5. Deduplication & Export
Merges distinct 2 km hotspots that fall within the same postal code. Generates the final, prioritized `final_blocked_pincodes.csv` that operations teams can use to prevent revenue leakage.

---

## Project Report

For a detailed breakdown of the methodology, business insights, and final operational recommendations, please refer to `Project_Report.pdf` included in the repository.
