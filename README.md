# Maine_Lakes_MIDAS
Access and export unique identifiers for Maine lakes and ponds, called MIDAS numbers

## Introduction
Maine lakes and ponds have unique identification numbers, assigned by state agencies,
which are known as "MIDAS" numbers.

Online metadata describes the MIDAS numbers as follows:
>  MIDAS numbers are unique identification numbers assigned in the 1970's
   to Maine lakes and ponds monitored and managed by Maine state agencies.
   A collaborative effort between MEDEP and MDIF&W provided an update to
   MIDAS numbers, for Maine lakes and ponds, in 2003.

Geospatial data on MIDAS numbers for Maine lakes and ponds used to be
available from the Maine Office of GIS.  MeOGIS's successor in providing
state-level geospatial data, the Maine Geolibrary, no longer provides 
a Maine Lakes data layer with MIDAS numbers, instead pointing users
to the National Hydrography Database (NHD) for all hydrographic features,
but the NHD does not contain MIDAS numbers.

We need the MIDAS numbers to connect water quality monitoring data unambiguously
with specific Maine Lakes, because many Maine lakes have similar names.

MEDEP and MWIF&W provide access to data on MIDAS centroids in several places. 
The data, however, is in GoogleEarth-flavored `*.kml`) and `*.kmz` files.
Manipultating that geospatial data requires parsing the `KML` files and 
exporting the data to simpler data formats.  Her, we use flat `*.csv` files.

## Contents
This archive contains downloaded `KML` files, code to extract data from those files,
shapefiles containing the same data, files in several formats containing subsets of the 
source data that pertain to the Casco Bay Watershed, and additional shapefiles that 
(for the Casco Bay Watershed) identify features from the NHD with MIDAS
codes.
