# Data Preparation

## Initial Geospatial Data
### Lake Centroids and MIDAS Numbers
The file `doc.kml` was extracted from the file `lakes_and_ponds.kmz`
by changing its extension to `.zip`, and unzipping in Windows.  We
discarded the graphic file giving the point symbol for lake centroids.

The python (3) script `DataParserLakes.py` scans that `.kml` file and extracts
data on Maine lakes, including centroid location (as longitude and latitude, WGS 
1984) and MIDAS number.  The script creates `lakes.csv`.  Both input and output
filenames are hard coded in the script, as are data column names.

We imported that data into ArcMap and converted it to the shapefile 
`Maine_Lakes`.  We then used Select By Location to select lakes with centroids
in the Casco Bay Watershed, and exported the resulting selection as the
shapefile `CB_Lakes`.

Note:  This point layer has multiple points with identical MIDAS numbers for
a handful of lakes, including
*  Sebago Lake, (Four points, MIDAS=5786),
*  Little Sebago (MIDAS=3714),
*  Otter Pond #1  (MIDAS=3402) and
*  Rich Millpond (MIDAS=3445).

This is corrected, below, in the related polygon data.

### Lake Geometry
A ponds Polygon Layer was generated in ArcGIS using "select by location" 
to select polygons from the national hydrography database (actually, we worked
with a "CB_Rivers_and_Ponds" layer, itself derived from the NHD). The selected
polygons were exported to a new shapefile called `CB_Ponds`.

Many of the NHD attributes were then deleted, since they are of no value in our
context, leaving us with the following:  
*  FID  
*  Shape  
*  OBJECTID  
*  GNIS_ID  
*  GNIS_Name  
*  AreaSqKm (no longer correct for Sebago)  
*  FType  
*  FCode  
*  Shape_Leng (no longer correct for Sebago)  
*  Shape_Area (no longer correct for Sebago)  

The CB_Ponds layer does not include the MIDAS numbers, so we conducted a
spatial join with the CB_Lakes point data.  We save the resulting joined table
as `CB_Ponds_MIDAS`. We deleted all columns except: GNIS_ID, GNIS_Name, and
Min_MIDAS

## Data QA/QC
We reviewed Maine lakes points (from `CB_Lakes`).that were not matched with a
spatial polygon (from `CB_Ponds_MIDAS`). We found several inconsistencies.  Most
involved small "ponds" or were considered wetlands or flowages that were not
included in the "NHD_Waterbody" layer that was the original source for 
`CB_Ponds_MIDAS`.

We could correct some inconsistencies by copying geospatial data from other
sources:  
*  Runaround Pond, North Yarmouth / New Gloucester was not matched.  The pond (a 
   narrow reservoir) was not included in the NHD Waterbody layer, but it
   was in the NHD Area layer. We Polygon copied from `CB_NHDArea` and added to
   `CB_Ponds_MIDAS`.  
*  Clark's Pond, South Portland, MIDAS 5638.  Polygon copied from `CB_NHDArea` and
   added to `CB_Ponds_MIDAS`.  
*  North Gorham Pond -- Gorham.  MIDAS 9705.  Polygon copied from `CB_NHDArea`
   and added to `CB_Ponds_MIDAS`.  
*  Unnamed Pond, Buxton, MIDAS 6955.  Polygon in `CB_NHD` coverages misses
   ~ 2/3 of pond, so dot does not intersect. Copied the missing polygon from
   `CB_NHDWaterbody`, and edited it to roughly match the aerial photograph.

But other inconsistencies remain uncorrected.  Results will not affect results,
as these are all either tiny ponds for which we have no monitoring data or 
small parts of larger basins.  
*  Little Sebago Lake, Upper Bay. MIDAS 3714. This is a second dot for part of
   the Lake, so a duplicate location for our purposes.  
*  Sebago Lake, MIDAS 5786. Sebago has four points in the original `KML` file.
   Three of them are part of he main Sebago Lake polygon, and so are duplicates
   for our purposes. The fourth point is addressed below.  
*  Coon Swamp Pond, Otisfield.  MIDAS 6452.  No apparent polygon in any layers. 
   Aerial photo looks like a wetland, not a pond. Omitted from the Polygon data.  
*  Dyer Ice Pond, Otisfield.  MIDAS 3442.  No apparent polygon in any layers.
   Also principally a wetland in the aerial photo. Omitted.  
*  Unnamed Pond, Standish, MIDAS 519.  No polygon included in `NHDArea` or
   `NHDWaterbody` -- probably classified as a wetland.  Omitted.  
*  Unnamed Pond, New Gloucester, MIDAS 8881.  No available polygon.  Mostly
   wetland flowage. Some aerials show shallow pond.  Omitted.
*  Unnamed Pond, Harrison.  MIDAS 8885.  Although there is a (fairly large)
   polygon for a river flowage, the aerial photo looks more like a river meadow
   than a pond.  The polygon would overstate size of what most people would consider
   a pond, so we omitted this Pond too. 
*  Unnamed Pond, Cape Elizabeth, MIDAS 8899. This is part of the stream channel
   heading to Great Pond, in marsh area.  Dropped.  


## Final Lakes and Ponds Data
Finally, we joined the attribute table based on MIDAS numbers with the
CB_Lakes attribute table, and saved the joined table as `CB_Ponds_MIDAS_Final`
(a shapefile). We deleted several attributes, leaving each pond with the
following attributes derived from the CB_Lakes data:
*  Name    (a Long Name that includes the Pond Name, the MIDAS Number, and the Town Name)
*  Longitude
*  Latitude
*  Name_1  (The Name of the Pond itself)
*  Town
*  MIDAS_Numb
*  Elevation
*  Notes

We merged some polygons that share MIDAS numbers and are part of single
lake basins. This involved the following steps:

1.  Little Sebago Lake, MIDAS has two points in the original `KML` file, but
    they correspond to the two major basins of the lake, and are part of one
    polygon in the NHD data source.  
2.  Sebago Lake has four points in the original `KML` file.  One of these is a
    tiny side basin of Sebago Lake, isolated by a road.  We merged it into the
    larger Sebago Lake polygon.  (The resulting "Sebago Lake" Multi-Polygon
    contains FOUR points from the Maine Lakes KML file, including one for the
    main lake, two major sub-basins, and the small side basin just merged into
    it.)  
3.  Otter Pond #1 is a small pond near the southwest corner of Sebago Lake that
    is divided by a road. We merged the two polygons into a multi-polygon.  
4.  Rich Millpond, to the west of Sebago Lake is another small pond apparently 
    divided by a road or causeway.  We merged the two sub-basins into a
    multi-polygon.

### NOTES
*  CB_Lakes is point data, and includes all MIDAS lakes in the watershed, even 
   those that lack corresponding polygons.
*  CB_Ponds_MIDAS_Final includes only those lakes for which we have polygons.
   The missing ponds were either absorbed into other ponds or are tiny.  We
   lack monitoring data for all of them.
