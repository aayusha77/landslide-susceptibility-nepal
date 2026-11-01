Landslide Susceptibility Mapping: Pasang Lhamu Highway, Nepal
A preliminary prototype that predicts where landslides are more likely along the Pasang Lhamu Highway corridor in Nepal, using terrain data and a random forest model with spatial cross-validation.

Overview
Area: Pasang Lhamu Highway corridor, Nepal
Task: binary classification (landslide point vs. "no landslide" point), then a susceptibility map (0 to 1)
Model: Random Forest (scikit-learn)
Validation: spatial cross-validation (0.1 degree blocks, 5-fold GroupKFold), scored with AUC
Data
Data	Source
Landslide points (2009 to 2018)	NASA High Mountain Asia Multitemporal Landslide Inventories (Amatya & Kirschbaum, 2021)
Elevation	Copernicus DEM GLO-30 (30 m)
Rainfall	CHIRPS v2.0 (0.05 degree), via NOAA ERDDAP
Rivers and roads	OpenStreetMap (Overpass API)
The raw data files are not included in this repository. Download them from the sources above.

Method
Landslide points: remove near-duplicates (same year, about 100 m) and leave out 2015 points (mostly triggered by the Gorkha earthquake). 529 points are used for training.
Terrain features from the DEM: elevation, slope, aspect, curvature, and a sunlight index.
"No landslide" points: random points inside the image footprint, within 3 km of a mapped landslide, at least 300 m from any known landslide, and inside the landslide elevation range.
Imaging bias check: almost all landslide points face south or southeast, which may come partly from shadows in optical satellite images. The "no landslide" points were therefore drawn from pixels with similar lighting, and slope direction (aspect) was left out of the final model.
Spatial cross-validation to avoid over-optimistic scores from clustered points.
Map 1 (terrain only): elevation, slope and curvature, applied to a grid of about 180 m spacing (70,331 points).
Extra features tested: rainfall, distance to rivers, distance to roads.
Map 2 (terrain + river distance): the same grid, with distance to the nearest river added, since it gave the biggest gain.
Results
Model	Mean AUC (spatial CV)
Terrain only (Map 1)	0.745
+ rainfall	0.767
+ river distance (Map 2)	0.797
+ road distance	0.744
+ rainfall + river + road	0.807
Distance to the nearest river gave the biggest gain, but this is an association, not proof of cause. OpenStreetMap rivers are incomplete, and river distance may partly reflect valley position.

Map 1: terrain only
Landslide susceptibility map, terrain only

Uses elevation, slope and curvature. Black dots are mapped landslides (2009 to 2018, without 2015).

Map 2: terrain + river distance
Landslide susceptibility map, terrain and river distance

Adds distance to the nearest river. High susceptibility follows river channels and valley floors more sharply than in Map 1, and distance to the nearest river is the most important feature (0.328), just ahead of slope (0.314). Some mapped landslides still fall in low-susceptibility areas, so the model misses some events.

Limitations
Landslide points end in 2018, so recent events are not included.
Two maps were made: terrain only, and terrain + river distance. Rainfall (a long-term mean at about 5 km resolution) and road distance were tested but not used in a map.
Land cover and geology were not tested.
"No landslide" points are random points, not confirmed stable ground.
The inventory was mapped from optical images, so some bias may remain.
Next steps
Add land cover and geology
Use rainfall from the year of each landslide
Validate against recent road failures
Data credits
Amatya, P. & Kirschbaum, D. (2021). High Mountain Asia Multitemporal Landslide Inventories, Version 1. NASA NSIDC DAAC.
Copernicus DEM GLO-30, European Space Agency / Copernicus.
Funk, C. et al. (2015). The Climate Hazards Infrared Precipitation with Stations (CHIRPS) dataset. Scientific Data.
River and road data © OpenStreetMap contributors (ODbL).
