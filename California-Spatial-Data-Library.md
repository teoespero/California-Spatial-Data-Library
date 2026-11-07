# California Spatial Data Library

Practice spatial programming with real California GIS data. Start with Monterey County, then use the source catalog below to build exercises for other parts of the state.

The starter GeoPackage opens in GeoPandas, QGIS, and ArcGIS Pro. The companion notebook covers reading layers, clipping roads to Salinas, calculating road mileage, joining earthquake points to census tracts, and exporting the results.

## Project files

| File | What's inside |
|---|---|
| `california-starter.gpkg` | Five vector layers in one GeoPackage |
| `California-Spatial-Programming.ipynb` | Step-by-step exercises for Colab or Jupyter |

## Quick start

### Google Colab

1. Extract the starter ZIP and open `California-Spatial-Programming.ipynb` in Colab.
2. In Colab's **Files** panel, upload `california-starter.gpkg` to the session's working folder.
3. Run the notebook from top to bottom. If a package is missing, run `%pip install geopandas matplotlib` in a new cell, then rerun the exercise.

### Local Python

Put the notebook and GeoPackage in the same folder. Open a terminal in that folder and run these commands.

```bash
python -m pip install geopandas matplotlib jupyterlab
python -m jupyterlab
```

Open the notebook in JupyterLab. To load your first layer from a Python session in that same folder, run this code.

```python
import geopandas as gpd

counties = gpd.read_file("california-starter.gpkg", layer="ca_counties")
monterey = counties.loc[counties["GEOID"] == "06053"].to_crs(3310)
print(monterey[["GEOID", "NAME"]])
```

## Data included

| Layer | Coverage | Geometry | Useful fields |
|---|---|---|---|
| `ca_counties` | California | Polygons | `GEOID`, `NAME` |
| `ca_places` | California incorporated places and census-designated places | Polygons | `GEOID`, `NAME`, `NAMELSAD` |
| `monterey_tracts` | Monterey County | Polygons | `GEOID`, `TRACTCE` |
| `monterey_roads` | Monterey County | Lines | `LINEARID`, `FULLNAME`, `MTFCC` |
| `central_coast_earthquakes` | Central Coast and nearby offshore waters | Points | `id`, `mag`, `depth_km`, `utc_time` |

The boundaries and roads come from [2024 Census TIGER/Line](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.2024.html). Census attributes are preserved. Keep GEOIDs as text to preserve leading zeros. TIGER places include cities and census-designated places, and some cross county lines.

The earthquake layer uses this [USGS catalog query](https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=2015-01-01&endtime=2024-12-31T23%3A59%3A59&minlatitude=35.7&maxlatitude=37.2&minlongitude=-123&maxlongitude=-120.2&minmagnitude=2.5). It covers 2015–2024 UTC, magnitude 2.5 or higher, 35.7–37.2° N and 123–120.2° W. Depth was moved from the point's Z coordinate into `depth_km`. The window includes offshore events, so some points will have no county match.

The GeoPackage stores all layers in WGS 84 (EPSG:4326). The notebook uses California Albers (EPSG:3310) for area and length. Source dates and coverage matter when combining layers.

## Find more California data

These are download and source pages. Start with a county, a watershed, or a few raster tiles when a full statewide download is large.

| Topic | Data and publisher | Format or access | What to try in Python |
|---|---|---|---|
| Boundaries and roads | [2024 TIGER/Line — U.S. Census Bureau](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.2024.html) | Shapefile ZIPs | Clip another county's roads and summarize mileage by MTFCC. |
| Local boundaries | [Monterey County GIS Open Data — County of Monterey](https://gis-montereyco.opendata.arcgis.com/) | ArcGIS portal and layer exports | Compare a local boundary with a Census boundary. |
| Public roads | [California All Public Roads Network — Caltrans](https://www.arcgis.com/home/item.html?id=2d56e65de89c418780056651640291e8) | ArcGIS feature service with GeoJSON queries | Clip roads to a study area, buffer the segments, and intersect the buffers with tracts. Includes public roads across California. |
| Agriculture | [Statewide Crop Mapping — California DWR](https://data.ca.gov/%40california-department-of-water-resources/statewide-crop-mapping) | Shapefile ZIP and map service | Clip a published crop year to Salinas and sum acres by crop class. Check whether the year is provisional. |
| Agriculture raster | [Cropland Data Layer — USDA NASS](https://www.nass.usda.gov/Research_and_Science/Cropland/SARS1a.php) | Classified raster | Count crop pixels by class within a study area. |
| Groundwater | [Bulletin 118 Groundwater Basins — California DWR](https://publish.data.ca.gov/dataset/i08-b118-ca-groundwaterbasins) | Vector dataset | Intersect crop polygons with groundwater basins. |
| Watersheds | [Watershed Boundary Dataset — USGS](https://publish.data.ca.gov/dataset/watershed-boundary-dataset-wbd) | Vector downloads | Group results by watershed code such as HUC12. |
| Wildfire | [Historical fire perimeters — CAL FIRE](https://data.ca.gov/%40cal-fire/california-fire-perimeters-all) | Vector download and service | Dissolve overlapping perimeters before measuring unique area burned. Historical coverage is incomplete. |
| Flooding | [National Flood Hazard Layer — FEMA](https://www.arcgis.com/home/item.html?id=50612954022a4b01839ac2ed75042365) | Map service and download options | Intersect mapped flood zones with road segments. |
| Earthquakes | [Earthquake Catalog — USGS](https://earthquake.usgs.gov/fdsnws/event/1/) | API and GeoJSON | Change the query bounds and cluster events using projected coordinates. |
| Protected lands | [California Protected Areas Database — GreenInfo Network](https://calands.org/get-data/) | Vector download | Calculate protected area by county and handle overlapping polygons. |
| Environmental indicators | [CalEnviroScreen 4.0 — OEHHA](https://oehha.ca.gov/calenviroscreen/download-data) | Spreadsheet and shapefile | Map tract scores. Match tract vintages before joining on GEOID. |
| Elevation | [3DEP — USGS National Map](https://www.usgs.gov/the-national-map-data-delivery) | DEM and lidar downloads | Use Rasterio to crop a DEM, then create a hillshade. |
| Coastal elevation | [Digital Coast Data Access Viewer — NOAA](https://coast.noaa.gov/digitalcoast/tools/dav.html) | Lidar, imagery, and land-cover downloads | Pick a small Monterey Bay coastal area and inspect its elevation and vertical datum. |
| Land cover | [Annual NLCD — MRLC](https://www.mrlc.gov/viewer/) | Classified raster | Compare land-cover class counts between two years. |
| Satellite imagery | [Landsat Collection 2 Surface Reflectance — USGS](https://www.usgs.gov/landsat-missions/landsat-collection-2-surface-reflectance) | Scene downloads through EarthExplorer | Mask cloud pixels, apply the published scale and offset, then calculate NDVI. |

## Keep your projects reproducible

For each layer you add, record the publisher, source URL, release date or query parameters, and the CRS. Keep the original download and write cleaned data to a separate file. In a notebook, show how you selected the study area so you can repeat the same steps for another county.