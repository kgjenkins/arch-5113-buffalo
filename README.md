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


## Civil Boundaries

Civil boundaries in New York state include things like counties, towns, villages, and cities.  We have these boundaries for all of New York state in a shapefile format, which is probably the most common geospatial data format found on the Internet.  But the shapefile format dates from the early 1990s, which is why there are multiple component files (.shp, .dbf, .shx, .prj, and sometimes others).  To add a shapefile to QGIS, we just need to select the .shp file and QGIS will take care of the rest.  Shapefiles also have other quirks, mostly notably a 10-character limit for attribute names.  Learn more at <http://switchfromshapefile.org/>

Load the administrative boundaries in QGIS:
* Look in the "data/boundaries" folder and drag the "Cities_Towns.shp" file onto the QGIS window

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
* 8th tab (placement) - horizontal mode tends to center the labels in the polygons
* 9th tab (rendering) - label every part of multi-part features (useful for streets)

To find out what CRS (coordinate reference system) is being used:
* Right-click > Properties...  Information tab


## Save your project!

Save "mymap.qgz" to the folder that also contains your data -- for example, within the "QGIS-workshops" folder. That way, you can zip up or move the containing folder around while keeping your map and data intact. The .qgz project file contains your map styles and pointers to your data files, but not the data itself.


## Streets

OpenStreetMap is a great source of streets data, and is widely considered to be the most complete and accurate street map in the world.  But New York State has very good official streets data that it makes available to the public, so we'll use a GeoPackage of Buffalo streets that was derived from a statewide dataset.  GeoPackage is a modern GIS format that was designed to improve upon the many limitations of shapefiles.

* Look in the "streets" folder, and drag the "streets_buffalo.gpkg" file onto QGIS
* If it ends up underneath the boundaries layer, drag it up above in the list of layers

Explore the data a bit (identify tool, attribute table), and notice the contents of the "SPEED" column -- click the header twice to reverse sort by this column.  It has values like 5, 15, 25, etc.  The definitions of these values can be found in the **data dictionary** (metadata) file for the NYS statewide streets dataset at <https://gis.ny.gov/system/files/documents/2023/07/streets_data_dictionary.pdf>

According to that document, the "SPEED" value refers not to the speed limit, but rather the average travel speed in miles per hour.  In any case, we can use these values to control the layer style and give prominence to the interstates and state highways.

* At the top of the layer styling panel, change "Single symbol" to "Categorized"
* Set Value = "SPEED", then click the "Classify" button towards the bottom of the panel

Random colors are assigned to each of the values, which is not really what we want.  You can shift-click to select all the symbols, then right-click to change the color for all the values at once.
* Change all the road colors to dark gray
* Double-click the "65" symbol to edit it -- change the width to 1.0mm
* Change the "35" and "45" widths to 0.7mm

This should have the effect of increasing the visual weight of the major roads, making it easier to distinguish different parts of the city.


## Basemaps via QuickMapServices

Basemaps are web-based map images designed by professional cartographers who have already done the hard work of aggregating different data layers (streets, rivers, buildings, etc.) and customizing styles and labels to work at different zoom levels.  Basemaps are usually global in scope, although there are some that only focus on certain regions.  They can be used to add context to your map, or just to help confirm that your data is correctly aligned.  The QuickMapServices plugin makes this easy, but you may need to install it first.  (It should already be installed on the Mann Library computers.)

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

Zoom in to explore how well your roads data aligns with the imagery.  It may be helpful to lighten the Google layer a bit:
* In the layer styling panel for the Google layer, click the transparency tab (2nd tab on the left)
* Set the opacity to about 50%

Turn your "Cities_Towns" boundaries layer back on, and adjust the style to work better with the basemap:
* Click "Simple fill", then set fill style = "No Brush"
* Set the stroke color to dark orange, width = 1.0mm

As we add more data, it may help to use a plainer basemap:
* Web menu > QuickMapServices > Stamen > Stamen Toner Lite
* Uncheck the Google Hybrid layer to hide it
* Adjust the other layer styles as needed

The Stamen Toner Lite layer is very light, so we can add our own streets on top if we want to emphasize the full road network.
* In the streets style panel, adjust Layer Rendering > Opacity to control the visual weight of the streets

Note that basemaps are just images.  You'll be able to include them when exporting your map as an image, but not when exporting data as a CAD format.


## Property parcels

Property parcels in the United States are usually maintained at the county level.  The parcels for Erie County have been downloaded, clipped to Buffalo, and saved into a geopackage format.

* Add the "parcels_buffalo.gpkg" file to your map
* When prompted, select all the layers and add them to your map (geopackages can contain multiple datasets, in this case there is also a table that explains the property class codes)

Explore the attributes using the "Identify Features" tool or by opening the attribute table.  Notice the "PROP_CLASS" field, which contains a 3-digit number that represents what type of property it is, such as 210='One family year-round residence', 311='Residential Vacant Land', 421='Restaurants'.  Brief descriptions can be found in the "nys_property_class_codes" layer that was included in the geopackage.  More detailed descriptions can be found at <https://www.tax.ny.gov/research/property/assess/manuals/prclas.htm>

Try creating a "Categorized" style to show vacant residential vacant land, using this expression for the value:
* `"PROP_CLASS" = '311'`

There will be two classes: 0 means false (the PROP_CLASS is not 311) and 1 means true (the PROP_CLASS is 311, so the property is vacant).  You can remove the "all other values" symbol, or just ignore it, since all the parcels will be either 0 or 1.


## Buildings

Microsoft has created building footprints using using computer vision algorithms on their Bing satellite imagery in several countries, including the US.  Try adding this dataset to your map, and set the style as follows:
* Black polygons with black borders
* Opacity 50%

Examine the map to see how well the absence of buildings lines up with parcels with PROP_CLASS 311.


## Trees

CSV (Comma-Separated Values) is a common format for tabular data.  If a spreadsheet has coordinate information like latitude and longitude, then it can be saved to CSV format and loaded into GIS software as a set of points.  Although it is possible to drag and drop a CSV file onto QGIS, it will only load as a table.  In order to have the data appear as points, do the following:
* Open the Data Source Manager (from the Layer menu, or from the icon in the toolbar)
* Select the "Delimited Text" tab on the left
* Click the "..." button at top right and select the "Tree_Inventory.csv" file in the "trees" folder
* Make sure the "File Format" is set to CSV
* Under "Record and Fields Options", check the boxes for "First record has field names" and "Detect field types"
* Under "Geometry Definition", select "Point coordinates" and make sure:
    * X field = Longitude
    * Y field = Latitude
    * Geometry CRS = EPSG:4326 - WGS 84 (standard latitude/longitude)

After clicking "Add", it make take a moment before the dots appear on your map.  (There are over 133,000 trees in this dataset!)

Explore the map, and note that these are generally just the trees along public roads that are maintained by the city.  Trees in private backyards are not usually included.


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

We won't have time to cover this in the workshop, but it is also possible to set up more complex page layouts, with grid lines, north arrow, scale bar, and additional text and images.  This is done using "Print layouts", found under the Project menu.  Once created, print layouts can also be exported to image files or PDFs.


## Exporting data layers to a DXF file (for CAD software)

Architects often want to export GIS layers for use in a CAD program like Rhino or AutoCAD.  Since DXF is a vector format, only vector layers can be exported -- sorry, no basemaps!  When exporting to CAD, be sure to set an appropriate CRS for the exported data, which means a CRS based on meters or feet -- not degrees longitude/latitude.
* Project menu > Import/Export > Export Project to DXF...
* Click the "..." button to specify where to save the .dxf file
* Be sure to select an appropriate CRS!  QGIS will reproject all your layers to the chosen CRS.  Some good CRS options for Buffalo include:
  * EPSG:2262 = NAD83 / New York West (if you want to work in US feet)
  * EPSG:32617 = WGS 84 / UTM zone 17N (if you want to work in meters)

Do **NOT** export to DXF using any of these CRS, or you will see distortions and incorrect measurements:
* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:4269 = NAD83 -- longitude/latitude, similar to WGS 84, but only defined for North America
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)

If you have data that extends beyond your area of interest, and don't want to export it all to DXF, try checking the "Export features intersecting the current map extent".  (You may need to cancel if you need to adjust the map extent, and then open the export dialog again after zooming into the area you want to export.)


## Elevation raster data

Various scales of elevation data are available for different parts of the world.  For much of the globe, the best available is 30-meter SRTM data, collected as part of the Shuttle Radar Topography Mission in 2000.  Most of the United States is covered by the National Elevation Dataset (NED) with 10-meter pixels.  Some places have higher resolution data available from a local lidar survey.  For Buffalo, there was a 2008 lidar survey which was used to generated a high-resolution elevation raster with 2-meter pixels.  Due to the relatively large size of these elevation datasets, the data has already been downloaded and clipped to the city of Buffalo.

* Open the `elevation` folder in your Windows file explorer or Mac finder
* Drag the "elevation_10m_buffalo.tif" and "elevation_2m_buffalo.tif" files onto your project

By default, raster data is displayed with a gray gradient, with higher values brighter.  We can change the style to bring out more details in the data.

* In the Layer Styling panel, change from "Singleband gray" to "Singleband pseudocolor"
* Try changing the color ramp to "Turbo" (click the small down-arrow to the right of the color bar)

It will be easier to adjust a small number of colorrs.  If you have more than 5-6 colors listed, try setting the Mode to "Quantile" and adjust the number of Classes to 6.

Notice the values assigned to each color.  You will probably want to adjust those values to better represent the lake water level, which is somewhere around 176-177.  Try the following, and notice how the map changes at each step:

* Double-click the value for light blue and set it to 176.4
* Set the green value to 176.5 and make it a dark green

Zoom into an area along the water.  With the settings above, any value 176.4 or less will be bluish, and anything just 176.5 and above that (land) will be green.  Notice that the water is not exactly flat, due to nature of the way the data was collected and created, with waves in the water adding noise to the elevation values.

## Value Tool plugin

Although we could use the "Identify" tool to click pixels and see their values, the "Value Tool" plugin will allow you to instantly show the values from raster layers without even having to click.

* Plugins > Manage and Install Plugins...
* Search all plugins for "value tool" and install it

The Value Tool will appear on your toolbar as a green circle with white "i".

* Click the Value Tool button to open the panel
* Check the box to enable the tool
* Move your cursor across the map to view the raster values

Try checking the elevations for various parts of the map.  Note that the elevations are in feet (which could be verified by reading the metadata file.)


## Elevation hillshade

Even though we can see the differences between the highest and lowest parts of the country, the area along the coast looks rather flat and devoid of detail.  Let's use a hillshade to made it look more 3-dimensional.

* In the Layers Panel, right-click the "elevation_2m_buffalo" layer > Duplicate
* Right-click "elevation_2m_buffalo copy" > Rename to "hillshade"
* Drag the "hillshade" layer name just above "elevation_2m_buffalo" and check the box to display the hillshade layer
* Open the styling panel be sure that you are styling the "hillshade" layer
* Change "Singleband pseudocolor" to "Hillshade"
* Set the blending mode to "multiply" (which will let us also see the colorized version underneath)

* Try turning the dial to adjust the angle of the "sun"

We can exaggerate the hillshade effect by increasing the Z Factor, which is usually the ratio between the vertical units (meters) and horizontal units (also meters).

* Set Z Factor to 3

Also notice that if you zoom in too far, you see individual pixel edges.  To avoid this effect:

* Set the resampling "Zoomed in" to "Bilinear" which will smooth out the pixels


## Generating contour lines

There is a "contours" display style for rasters, but is just a visual effect.  If we want to export 3D contour lines for use in a program like Rhino or AutoCAD, we will need to create a vector contour layer.

* Open the processing toolbox (gear icon, or in the Processing menu)
* Search for "contour" and open the "Contour" tool under GDAL > Raster extraction
* Set the following parameters:
  * Input layer = "elevation_2m_buffalo"
  * Interval between contour lines = 2 (this value, which is meters in this case, should usually not be much less than the pixel size)
  * Check the box to "Produce 3D vector" (if you don't see it, expand the Advanced Parameters section)
* Keep the other options and click "Run"

To export the 3D contours to DXF, right-click the layer > Export > Save features as ... AutoCAD DXF or else export your whole project to DXF.


## Citing your Data Sources

The data for this workshop was derived from the following sources:

* Boundaries: <https://data.gis.ny.gov/maps/nys-civil-boundaries/about>
* Buildings: <https://cugir.library.cornell.edu/catalog/cugir-009125>
* Elevation: <https://orthos.dhses.ny.gov/>
* Parcels: <https://gis.ny.gov/parcels>
* Streets: <https://data.gis.ny.gov/datasets/sharegisny::nys-streets/about>
* Trees: <https://data.buffalony.gov/Quality-of-Life/Tree-Inventory/n4ni-uuec>

If you are using a basemap, you'll want to provide attribution to the basemap provider.  For some of the basemaps, the layer properties may have these details and possibly a link to the terms of use.  For example:

* Stamen Toner Lite > right-click > Properties > QGIS Server tab
  > Map tiles by Stamen Design, under CC BY 3.0. Data by OpenStreetMap, under ODbL
* OSM Standard > right-click > Properties > QGIS Server tab
  > © OpenStreetMap contributors, CC-BY-SA

