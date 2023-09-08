# QGIS workshop for ARCH 5113 (Dietzsch), focusing on Buffalo, NY

Workshop 2023-09-08 by Keith Jenkins, GIS Librarian at Mann Library, Cornell University. \
This document is online at: <https://kgjenkins.github.io/arch-5113-buffalo/>

For help after this workshop, contact me at kgj2@cornell.edu  
Or set up a Zoom appointment at <https://guides.library.cornell.edu/gis/help>

All the data and documentation for this workshop can be downloaded from: \
<https://github.com/kgjenkins/arch-5113-buffalo/archive/main.zip>


## Some GIS terminology and concepts

**GIS** stands for Geographic Information System (or Science)

"GIS data" refers to data with a spatial component -- it can be mapped!

**QGIS** (<https://qgis.org/>) is one of several popular GIS programs for mapping and spatial analysis.  It is free, open-source desktop software that runs on Windows, Mac, and Linux.  QGIS is created by developers around the world, supported by municipal and national governments, corporations, user groups, and individual users.  Discussions about new features, bug fixes, and future directions happen on [e-mail lists](https://qgis.org/en/site/getinvolved/mailinglists.html) and the [project's GitHub organization](https://github.com/qgis).

Questions about how to use QGIS are typically asked and answered on [GIS StackExchange](https://gis.stackexchange.com/).  If you search the web for QGIS questions, there is a good chance you'll end up finding the answer already there, so always be sure to search before asking a new question.

**Vector** = points, lines, polygons
Common file formats are GeoPackage (.gpkg) and Shapefile (.shp, .dbf, .prj, etc.)

**Raster** = pixels (images, but also data)
Common file format is GeoTIFF (.tif)

Vector data does not usually contain any information about how to style the display of the data.

Styles are added in a map project.  The same data could be styled in different ways in different maps, or even in the same map!

The QGIS project file .qgz contains your styles and pointers to the data, but does not include the data itself.  I recommend keeping everything within a containing folder (there can be subfolders) that you can zip up or save to a device or the cloud.  Something like this:

* myproject/
  * data/
    * boundaries/
      * counties.gpkg
      * states.gpkg
    * buildings.gpkg
    * streets.shp
  * notes-about-sources.txt
  * mymap.qgz

**CRS** = Coordinate Reference System

Ideally, CRS details are quietly handled behind the scenes, so we barely even need to think about it.

But in the real world, you'll eventually run into CRS puzzles and problems, and CRS becomes even more important when doing distance- or area-based analysis.  Here are some of the CRS you might see in western NY:

* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:4269 = NAD83 -- longitude/latitude, similar to WGS 84, but based on a 1983 model of the Earth only defined for North America
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)
* EPSG:2262 = NAD83 / New York West -- one of many "state plane" coordinate systems measured in feet
* EPSG:26717 = NAD27 / UTM zone 17N -- meter-based system based on a 1927 model of the Earth only defined for North America
* EPSG:26917 = NAD83 / UTM zone 17N -- meter-based system based on a 1983 model of the Earth only defined for North America
* EPSG:32617 = WGS 84 / UTM zone 17N -- meter-based system based on a 1984 model of the Earth used by GPS satellites


## Municipal Boundaries

Municipalities in New York state include towns, villages, and cities.  We have boundary data for these boundaries in Cattaraugus county in a shapefile format, which is probably the most common geospatial data format found on the Internet.  But the shapefile format dates from the early 1990s, which is why there are multiple component files (.shp, .dbf, .shx, .prj, and sometimes others).  To add a shapefile to QGIS, we just need to select the .shp file and QGIS will take care of the rest.  Shapefiles also have other quirks, mostly notably a 10-character limit for attribute names.  Learn more at <http://switchfromshapefile.org/>

Load the administrative boundaries in QGIS:
* Look in the "data/bourdaries" folder and drag the "Cattaraugus_muni_boundaries.shp" file onto the QGIS window

Change the layer style:
* Open the Layer Styling panel – colorful paintbrush icon at top left of the Layers panel
* Change the color
* Click "Simple fill" to change other properties, like stroke color and width

Get information about a polygon:
* Click "Identify Features" tool, select the layer, then click a polygon

View information about all the polygons as a table:
* Right-click layer in Layers pane > Open Attribute Table
* Click on a column header to sort by that column

The table and map are linked, so select a row from the table (by clicking the row number) to see it highlighted in yellow on the map.  There is a toolbar button to "Deselect features from all layers".

Add labels by clicking the label tab (yellow "abc") in the styling panel
* Single Labels, value = "NAME"
* 9 tabs of options!
* 1st tab (text) - font, size, color
* 2nd tab (formatting) - wrap lines to 12 characters
* 9th tab (rendering) - label every part of multi-part features (useful for Allegany Reservation)

To find out what CRS (coordinate reference system) is being used:
* Right-click > Properties...  Information tab


## Save your project!

Save "mymap.qgz" to the folder that also contains your data -- for example, within the "QGIS-workshops" folder. That way, you can zip up or move the containing folder around while keeping your map and data intact. The .qgz project file contains your map styles and pointers to your data files, but not the data itself.


## Streets

OpenStreetMap is a great source of streets data, and is widely considered to be the most complete and accurate street map in the world.  But New York State has very good official streets data that it makes available to the public, so we'll use that.

* Look in the "streets" folder, and drag the "Cattaraugus_e911_roads.shp" file onto QGIS
* If it ends up underneath the boundaries layer, drag it up above in the list of layers

Explore the data a bit (identify tool, attribute table), and notice the contents of the "SHIELD" column -- click the header twice to reverse sort by this column.  It has values like 'C', 'I', 'P', 'S', etc.  The definitions of these values can be found in the **metadata** file for the NYS statewide streets dataset at <http://gis.ny.gov/gisdata/supportfiles/Streets-Data-Dictionary.pdf>

We can use those values to control the layer style and give prominence to the interstates and state highways.

* At the top of the layer styling panel, change "Single symbol" to "Categorized"
* Set Value = "SHIELD", then click the "Classify" button towards the bottom of the panel

Random colors are assigned to each of the values, which is not really what we want.  You can shift-click to select all the symbols, then right-click to change the color for all the values at once.
* Change all the road colors to black
* Double-click the "I" (interstate) symbol to edit it -- change the width to 1.5mm and color it dark blue
* Change the 'C', 'P', 'S', and 'U' widths to 0.75mm


## Basemaps via QuickMapServices

Basemaps are web-based map images designed by professional cartographers who have already done the hard work of aggregating different data layers and customizing styles and labels to work at different zoom levels.  Basemaps are usually global in scope, although there are some that only focus on certain regions.  They can be used to add context to your map, or just to help confirm that your data is correctly aligned.  The QuickMapServices plugin makes this easy, but we need to install it first.

* Plugins menu > Manage and Install Plugins...
* Scroll down, or search all plugins for "quickmap" (you don’t need to type the whole name)
* Select the QuickMapServices plugin
* Click the "Install plugin" button (it installs in seconds)
* Click "Close"

When you first install QuickMapServices, you'll want to get the full set of basemap definitions.

* Web menu > QuickMapServices > Settings
* "More services" tab > "Get contributed pack"

To add the Google Hybrid basemap, which combines aerial photos with placename labels:

* Web menu > QuickMapServices > Google > Google Hybrid

You may need to turn off your boundaries layer to see the basemap.

Most basemaps are in a different projection (CRS) called Pseudo- or Web-Mercator, EPSG:3857.  QGIS is reprojecting it to match the CRS of the first dataset we loaded, which can cause some pixelation, making the labels hard to read.  If you zoom out, you'll notice that the US looks stretched out.

To avoid the pixelization and the stretched shapes, we can set our map to use the basemap CRS:
* Right-click the Google layer > Set CRS > Set Project CRS from Layer
* Right-click the Google layer > Zoom to Native Resolution (100%)

Zoom in to explore how well your roads data aligns with the imagery.

Turn your boundaries layer back on, and adjust the style to work better with the basemap:
* Click "Simple fill", then set fill style = No Brush
* Set the stroke color to orange, width = 2mm

As we add more data, it may help to use a plainer basemap:
* Web menu > QuickMapServices > Stamen > Stamen Toner Lite
* Uncheck the Google Hybrid layer to hide it
* Adjust the other layer styles as needed (white buffer, for example)

The Stamen Toner Lite layer is very light, so we can add our own streets on top if we want to emphasize the full road network.
* In the streets style panel, adjust Layer Rendering > Opacity to control the visual weight of the streets


## Allegany Reservation

The Allegany Reservation is included in the boundaries layer.  To highlight it on the map:

* Use a "Categorized" style for the boundary layer (Value = "CLASS")
* Double-click the style for 'Z1' and change it to green fill, with 50% opacity


## Property parcels

Property parcels in the United States are usually maintained at the county level.  The parcels for Cattaraugus County have been downloaded and saved into a geopackage format, which is a modern GIS vector format that was designed to improve upon the many limitations of shapefiles.

* Add the "cattaraugus_parcels.gpkg" file to your map

Explore the attributes using the "Identify Features" tool or by opening the attribute table.  Notice the "CLASS_CODE" field, which contains a 3-digit number that represents what type of property it is, such as 210='One family year-round residence', 120='Field crops', 421='Restaurants'.  A description of these values can be found at https://www.tax.ny.gov/research/property/assess/manuals/prclas.htm

Try creating a "Categorized" style to show the major classes of property types based on the first digit, using this "Value":
* `left(CLASS_CODE, 1) || 'xx'`



## Exporting your map to an image file

To export the current map view to an image file:
* Project menu > Import/Export > Export Map to Image...
* Update the extent or leave as is
* Increase the resolution (try 300dpi)
* Save as a .png file


## Exporting your map to a PDF file

Exporting to PDF is useful if you are going to print your map, or if you want to do further work in a program like Inkscape or Adobe Illustrator.  The PDF will keep separate layers for each data layer.  Vector layers will remain vectors, and raster layers will remain raster.

* Project menu > Import/Export > Export Map to PDF...
* Check the settings, especially the resolution if your map includes raster layers, including basemaps


## Exporting data layers to a DXF file (for CAD software)

Architects often want to export GIS layers for use in a CAD program like Rhino or AutoCAD.  Since DXF is a vector format, only vector layers can be exported -- sorry, no basemaps!  When exporting to CAD, be sure to set an appropriate CRS for the exported data, which means a CRS based on meters or feet -- not degrees longitude/latitude.
* Project menu > Import/Export > Export Project to DXF...
* Click the "..." button to specify where to save the .dxf file
* Be sure to select an appropriate CRS!  QGIS will reproject all your layers to the chosen CRS.  Some good CRS options for Salamanca include:
  * EPSG:2262 = NAD83 / New York West (ftUS)
  * EPSG:32617 = WGS 84 / UTM zone 17N (meters)

Do **NOT** export to DXF using any of these CRS, or you will see distortions and incorrect measurements:
* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:4269 = NAD83 -- longitude/latitude, similar to WGS 84, but only defined for North America
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)

If you have data that extends beyond your area of interest, and don't want to export it all to DXF, try checking the "Export features intersecting the current map extent".  (You may need to cancel if you need to adjust the map extent, and then open the export dialog again.)


## Adding data from ArcGIS Online

<https://www.arcgis.com/home/search.html?q=owner%3A%22tlaquay%22>


## Citing your Data Sources

The data for this workshop was derived from the following sources:

* Boundaries: <https://data.gis.ny.gov/maps/nys-civil-boundaries/about>
* Elevation: <https://cugir.library.cornell.edu/catalog/cugir-008186>
* Parcels: <https://gis.ny.gov/parcels>
* Streets: <https://data.gis.ny.gov/datasets/sharegisny::nys-streets/about>
* Trees: <https://data.buffalony.gov/Quality-of-Life/Tree-Inventory/n4ni-uuec>

If you are using a basemap, you'll want to provide attribution to the basemap provider.  For some of the basemaps, the layer properties may have these details and possibly a link to the terms of use.

* Stamen Toner Lite > right-click > Properties > QGIS Server tab
  > Map tiles by Stamen Design, under CC BY 3.0. Data by OpenStreetMap, under ODbL
* OSM Standard > right-click > Properties > QGIS Server tab
  > © OpenStreetMap contributors, CC-BY-SA

