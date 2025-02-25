# R-Projections-Workshop

This is a workshop on understanding and using projections for spatial data in R.

## Expected Outcomes

The expected outcome of this workshop is not that you will understand everything about projections by the end of the 2 hours.  Learning and understanding projections for geospatial data is (perhaps unfortunately) a life-long endevor.  My expectation is that by the end of this workshop, you will have a better understanding of what a projection is, why you would choose one over another, and how to apply them correctly to geospatial data in R.  Will you have questions later?  Of course!  That is entirely expected.

## Classroom Expectations

Learning is sometimes difficult.  Projections are confusing.  As I stated early, I hope to mitigate this, but let's recognize it up front.  I have some rules for my classroom for just these kinds of situations:

1. I expect you to ask questions. 
1. I want you to ask "dumb" questions as well as "smart" questions (whatever those terms mean for you).  All questions are valid.
1. You may answer questions.  I highly encourage you to discuss your questions with others in the room.  Maybe you can figure it out together.  If you want more input, ask someone else.
1. I will do my best to answer your questions, but reserve the right to say "I don't know" or "I will need to do some research and get back to you" or ask the people in the room if they know the answer.  One person can't know everything.
1. You can and should take breaks when you need to.  Step outside, walk around, I won't be offended.  Brains need a break.  If you need to take this home and work on it later, that's fine too!


# Coordinate Reference System (CRS)

**CRS = Datum + Projection + Additional Parameters**

*A common analogy employed to teach projections is the orange peel analogy.  If you imagine that the earth is an orange, how you peel it and then flatten the peel is similar to how projections get made.   We will also use it here.*

## Datum

A **Datum** is a model of the shape of the earth.

It has angular units (i.e. degrees) and defines the starting point (i.e. where is (0,0)?) so the angles reference a meaningful spot on the earth.

Common global datums are WGS84 and NAD83.  Datums can also be local, fit to a particular area of the globe, but ill-fitting outside the area of intended use.

When datums are used by themselves it's called a *Geographic Coordinate System*.

*Orange Peel Analogy: a datum is your choice of fruit to use in the orange peel analogy.  Is the earth an orange, a lemon, a lime, a grapefruit?*

![Citrus fruit on display at the market](https://farm3.staticflickr.com/2260/2508805118_500f5bba28_n.jpg)

Comments from the pros: Datums matter! California Albers with NAD27 is **NOT** the same as California Albers with NAD83.  See all of the advice [here](https://twitter.com/MicheleTobias/status/955861280174174208)

## Projection

A **Projection** is a mathematical transformation of the angular measurements on a round earth to a flat surface (i.e. paper or a computer screen).

The units associated with a given projection are usually linear (feet, meters, etc.).

Many people use the term "projection" when they actually mean "coordinate reference system". (With good reason, right?  Coordinate References System is long and might make you sound pretentious.)  One example is the title of this workshop... but you wouldn't know what it was about if I said it was a workshop on "Coordinate Reference Systems in R", would you?

*Orange Peel Analogy: a projeciton is how you peel your orange and then flatten the peel.*

![An orange peeled like a map projection](http://blogs.lincoln.ac.nz/gis/wp-content/uploads/sites/16/2017/03/laranjoide_1.jpg)

Image source: http://blogs.lincoln.ac.nz/gis/2017/03/29/where-on-earth-are-we/


## Additional Parameters

Additional parameters are often necessary to create the full coordinate reference system.  For example, one common additional parameter is a definition of the center of the map.  The number of required additional parameters depends on what is needed by each specific projection.

*Orange Peel Analogy: an additional parameter could include a definition of the location of the stem of the fruit.*

## Which CRS/projection should I use?

To decide if a projection is right for your data, answer these questions: 
1. What is the area of minimal distortion?
2. What aspect of the data does it preserve?

[University of Colorado's Map Projections](https://www.colorado.edu/geography/gcraft/notes/mapproj/mapproj_f.html) and the [Department of Geo-Information Processing](http://kartoweb.itc.nl/geometrics/map%20projections/mappro.html) has a good discussion of these aspects of projections.  Online tools like [Projection Wizard](http://projectionwizard.org/) can also help you discover projections that might be a good fit for your data.

Comments from the pros: Take the time to figure identify a projection that is suited for your project.  You don't have to stick to the ones that are popular.


# Notation for Coordinate Reference Systems in R

You have two options for identifying a CRS in most R commands.  The documentation for a command that requires projection information will tell you which is required.  Often you can choose between the two options.

## EPSG Code

*A note on linguistics:* EPSG stands for "European Petroleum Survey Group"... but everyone just says EPSG.

An EPSG Code is an ID that has been assigned to most common projections to make reference to a particular projection easy.  An EPSG Code is also called an SRID (Spatial Reference Identifier).  Technically, EPSG is the authority that assigns SRIDs, but you will hear these terms used interchangibly.

The main advantages to using this method of specifying a projection are that it is standardized and ensures you have the same parameters every time.  The disadvantage is that if you need to know the parameters used by the projection or it's name, you have to look them up, but that's fairly easy to to at [spatialreference.org](http://spatialreference.org/ref/epsg/).  Also, you can't customize the parameters if you use an EPSG code.  For example: `EPSG:27561`

## PROJ.4 String

PROJ.4 is an open source library for defining and converting between coordinate reference systems.  It defines a standard way to write projection parameters.  For example: `+proj=lcc +lat_1=49.5 +lat_0=49.5 +lon_0=0 +k_0=0.999877341 +x_0=6 +y_0=2 +a=6378249.2 +b=6356515 +towgs84=-168,-60,320,0,0,0,0 +pm=paris +units=m +no_defs`

Two important advantages to using this option are (1) the parameters are human-readable and immediately transparent and (2) the strings are easily customized.  The main disadvantage to this option is that it's easy to make a mistake when you reproduce the string, accidentally changing parameters.

*A note on linguistics:* PROJ.4 is commonly pronounced "prodge four" ("PROJ" rhymes with "dodge"); PROJ is short for "projection".  The 4 is because we're currently using the 4th version of this library.

# The BIGGEST Mistake
The #1 biggest mistake I see in any GIS (ArcMap, QGIS, R, GRASS, Python, etc.) is defining a projection for a dataset when the person should have re-projected the data.  It is very common that you'll need to tell your GIS what the projection/CRS of your data should be.  In these cases, the GIS needs to know what the projection/CRS **currently** is, not what you would like it to be.  If you need to change a projection, you need to go through a different process, often called Re-project or Transform.



---------------------------------------
# Hands-On Tutorial
Finally, now we're going to work in R to get some hands-on practice with the concepts we just discussed.

We'll be working with sp objects today, but you should also know that there are sf objects as well.  [Roger Bivand's review of spatial data for R](https://cran.r-project.org/web/views/Spatial.html) is helpful for understading the options for working with spatial data.

## Download the data

The data for this workshop is available in the [data folder](https://github.com/MicheleTobias/R-Projections-Workshop/tree/master/Data) in this repository, or you can [download a .zip file from FigShare](https://figshare.com/s/f68e06c4177e27d0aa47).

## Start R
Pick the R flavor of your choice - regular R, R Studio, etc. - and start it up.  You can either add the commands we'll be using to a script file to run, or you can just run the lines individually in your console to see how they work.  It's up to you.

## Load the libraries we'll need.
TIP: I add to this section as I write my code and need new libraries. Keep them in one place for easy reference.

```
install.packages("sf")

library("sf")
```

## Set your working directory to the folder where you saved your data
TIP: Windows users, use \\\ instead of \ or switch the direction of the slashes to /... it has to do with escape characters.

```
setwd("C:/Workshops/Data")
```
Replace C:/Workshops/Data with the path to the folder in which you saved your data.

## Read the data into our R session
Because "points" by itself is a command and we don't want confusion, I've added "ws." (WS for watershed) to the front of my variable names.

Most of the vector data formats can be read into r with the ```st_read``` function. To see the help files for this function, run ```?st_read``` in your console.

```
ws.points<-st_read("data/WBDHU8_Points_SF.geojson")
ws.polygons<-st_read("data/WBDHU8_SF.geojson")
ws.streams<-st_read("data/flowlines.shp")
```


Let's look at the contents of one of the files:

```
ws.polygons
```

Note that the output not only shows the data but gives you some metadata as well, such as the geometry type, bounding box (bbox), and the projected CRS, which is "NAD83 / California Albers".


## Indentifying the Assigned CRS
To see just the coordinate reference system, we can use the crs() command.
Let's check the CRS for each of our files:

```
st_crs(ws.points)
st_crs(ws.polygons)
st_crs(ws.streams)
```

It looks like the streams dataset does not have its CRS defined.  

## Defining a CRS
Let's be clear that the streams data *has* a CRS, but R doesn't know what it should be.  (Someone may have forgotten to include the .prj file in this shapefile.)  You don't get to decide what you want it to be, but rather figure out what it *IS* and tell R which coordinate system to use.  How do you  know which CRS the data has if its not properly defined?  Typically, you first ask the person who sent it.  If that fails, you can search for another version of the data online to get a file with the correct projection information.  Finally, outright guessing can work, but isn't recommended.  We know that the CRS for this data should be EPSG 3309 (because that's what the instructor saved it as before she deleted the .prj file... shapefiles are not a great exchange format, FYI).

Set the CRS:

```
ws.streams.3309<-st_set_crs(ws.streams, value=3309) 

```

EPSG 3309 = California Albers, NAD 27

Let's imagine we loaded up our data and find that it shows up on a map in the wrong location.  What happened?  Someone defined the CRS incorrectly.
How do you fix it?  First you figure out what the CRS should be, then you run one of the lines above with the correct CRS definition to fix the file.

## Tranforming / Reprojecting Vector Data
We need to get all our data into the same projection so it will plot together on one map before we can do any kind of spatial process on the data.

```
# tranform/reproject vector data
ws.streams.3310<-st_transform(ws.streams.3309, crs=3310)

#   another option: match the CRS of the polygons data
ws.streams.3310<-st_transform(ws.streams.3309, crs=st_crs(ws.polygons))
```

Let's check the CRS for each of our files again:

```
st_crs(ws.points)
st_crs(ws.polygons)
st_crs(ws.streams)
```
Now they all should match.

## Plotting the Data
Lets make a map now that all our data is in the same projection.  What if we had tried to do this earlier before we fixed out projection definitions and transformed the files into the same projections?

Load up the CA Counties layer to use as reference in a map:

```
ca.counties<-st_read("data/CA_Counties.geojson")
```

Let's plot all the data together:
```
plot(
  ca.counties$geometry, 
  col="#FFFDEA", 
  border="gray", 
  xlim=st_bbox(ws.polygons)[1:2], 
  ylim=st_bbox(ws.polygons)[3:4], 
  bg="#dff9fd",  
  main = "Perennial Streams",
  sub = "in the San Francisco Bay Watersheds"
  )
plot(ws.streams$geometry, col="#3182bd", lwd=1.75, add=TRUE)
plot(ws.points$geometry, col="black", pch=20, cex=3, add=TRUE)
plot(ws.polygons$geometry, lwd=2, border="grey35", add=TRUE)
```
Some explanation of the code above for plotting the spatial data:
* ```xlim/ylim``` sets the extent.  Here I used the numbers from the bounding box of the polygon dataset, but you could put in numbers - remember that this is projected data so lat/long won't work
* ```add=TRUE``` makes the 2nd, 3rd, 4th, etc. datasets plot on the same map as the first dataset you plot - order matters
* ```col``` sets the fill color for the geometry
* ```border``` sets the outline color (or stroke for users of vector graphics programs)
* ```bg``` sets the background color for the plot
* colors can be specified with words like "gray" or html hex codes like #dff9fd

# Raster Data

We've just looked at how to work with the CRS of vector data. Now let's look at raster data.

First, we need to install the package that works with raster data. Today we'll use *terra* but there are other packages like *raster* that also work in similar ways.

```
install.packages("terra")
library(terra)
```
Now we'll load our data. This is a digital elevation model (DEM) of the City of San Francisco.

```
dem<-rast(x="data/DEM_SF.tif")
```

What is the CRS of this dataset? Note that the command to read the CRS in the *terra* package is different from *sf*.

```
crs(dem)
```

Our data came with a CRS, but in the event that we needed to define it, we would do it like this:

```
crs(dem)<-"epsg:4269"
```

So we know that our data is in the CRS with EPSG code 4269. That's not the same CRS as our other data, so we'll need to transform it. Again, *terra* uses different names than *sf* does. Is this confusing? Yes it is. This is why we read the documentation.

```
dem.3310<-project(dem, "epsg:3310")
```

Now that all of our datasets are in the same projection, we can use them together. Let's make a map with both our raster and vector data.

```
plot(dem.3310, col=terrain.colors(50), axes = FALSE, legend = FALSE)
plot(ws.streams.3310$geometry, col="#3182bd", lwd=3, add=TRUE)
plot(ws.polygons$geometry, lwd=1, border="grey35", add=TRUE)
```

# Conclusion
After completing this workshop, you should now have a better understanding of Coordinate Reference Systems (CRS) or projections, as they are often called colloquially.  You can now find out what the CRS is for a dataset and know the common formats this can take.  You should understand the difference between defining a CRS and tranforming a dataset (often called "reprojecting" in other GIS programs), when to use them, and how to execute both commands.  You've also seen how to use the basic plot() function to make a map.

Now that you've had some hands-on experience with projections and spatial data, it's a good time to go back an review the concepts introduced in the beginning of the workshop.  You may find that some of it makes more sense now that you have more experience.

Do you now feel like you know everything you need to know and will **never** have any more questions?  Of course not!  It's a learning process that will continue for the rest of your career working with spatial data.  Need more help?  See data.ucdavis.edu for how to contact your friendly UC Davis GIS Data Curator.


---------------------------------------
# Resources Used to Compile this Tutorial:

1. [Geocomputation with R](https://geocompr.robinlovelace.net/) by Robin Lovelace
1. [Rspatial.org](http://rspatial.org/spatial/rst/6-crs.html)
1. [Data Carpentry Intro to Geospatial Data with R](http://www.datacarpentry.org/R-spatial-raster-vector-lesson/)
1. [University of Colorado's Map Projections](https://www.colorado.edu/geography/gcraft/notes/mapproj/mapproj_f.html)
1. [International Institute for Geo-Information Science and Earth Observation (ITC)](http://kartoweb.itc.nl/geometrics/map%20projections/mappro.html)
1. [Carlos Furuti's Projections Page](http://www.progonos.com/furuti/MapProj/Normal/TOC/cartTOC.html)
1. [ESRI Resource Center - Projections](http://help.arcgis.com/en/geodatabase/10.0/sdk/arcsde/concepts/geometry/coordref/coordsys/projected/mapprojections.htm#:~:text=False%20easting%20is%20a%20linear,origin%20of%20the%20y%20coordinates.&text=For%20example%2C%20if%20you%20know,a%20false%20northing%20of%20%2D5%2C000%2C000.)

Map Projection Fun:

1. [xkcd's Map Projections](https://xkcd.com/977/)
1. [Jason Davies' Map Projection Transitions](https://www.jasondavies.com/maps/transition/)
1. [Carlos Furuti's Printable Projections](http://www.progonos.com/furuti/MapProj/Normal/ProjPoly/Foldout/foldout.html) Global projections you can print and assemble


