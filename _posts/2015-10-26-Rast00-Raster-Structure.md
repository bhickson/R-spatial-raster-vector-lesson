---
layout: post
title: "Lesson 00: About Raster Data"
date:   2015-10-29
authors: [Kristina Riemer, Zack Brym, Jason Williams, Jeff Hollister,  Mike Smorul, Leah Wasser]
dateCreated:  2015-10-23
lastModified: 2015-10-23
category:  
tags: [module-1]
mainTag: GIS-Spatial-Data
description: "This post explains the fundamental principles, functions and metadata that you need to work with raster data in R."
code1: 
image:
  feature: NEONCarpentryHeader_2.png
  credit: A collaboration between the National Ecological Observatory Network (NEON) and Data Carpentry
  creditlink: http://www.neoninc.org
permalink: /R/Introduction-to-Raster-Data-In-R
comments: false
---

{% include _toc.html %}

##About
This activity will walk you through the fundamental principles of working 
with raster data in R.

**R Skill Level:** Intermediate - you've got the basics of `R` down.

<div id="objectives" markdown="1">

###Goals / Objectives

After completing this activity, you will know:

* What a raster dataset is and its fundamental attributes.
* How to import rasters into `R` using the `raster` library.

###Things You'll Need To Complete This Lesson

###R Libraries to Install:

* **raster:** `install.packages("raster")`
* **rgdal:** `install.packages("rgdal")`

####Tools To Install

Please be sure you have the most current version of `R` and preferably
R studio to write your code.


####Data to Download

Download the workshop data:

* <a href="http://figshare.com/articles/NEON_AOP_Hyperspectral_Teaching_Dataset_SJER_and_Harvard_forest/1580086" class="btn btn-success"> DOWNLOAD Sample NEON LiDAR data in Raster Format & Vegetation Sampling Data</a> 

The LiDAR and imagery data used to create the rasters in this dataset were 
collected over the Harvard and San Joaquin field sites 
and processed at <a href="http://www.neoninc.org" target="_blank" >NEON </a> 
headquarters. The entire dataset can be accessed by request from the NEON website.

####Recommended Pre-Lesson Reading

* <a href="http://cran.r-project.org/web/packages/raster/raster.pdf" target="_blank">
Read more about the `raster` package in R.</a>

</div>

#About Raster Data
Raster or "gridded" data are data that are saved on a regular grid which is rendered
on a map as pixels. Each pixel contains a value that represents an area on the Earth's 
surface.

<a href="{{ site.baseurl }}/GIS-Spatial-Data/Working-With-Rasters/"> More on 
rasters here</a>. 

![COLINS IMAGE HERE ]({{ site.baseurl }}/images/raster_concept.png)

#Types of data stored as rasters

Raster data can be continuous or categorical. Continuous rasters can have a 
range of values. Some examples of continuous rasters include

1. Precipitation maps
2. Maps of tree height derived from lidar data
3. Elevation values for a region. 

A map of elevation for Harvard Forest, derived from the NEON AOP lidar sensor
is below.

![ ]({{ site.baseurl }}/images/rfigs/00-Raster-Structure/elevation-map-1.png) 

We can also classify continuous rasters to identify regions that are low, medium
and high elevation. Some examples of classified maps include

1. landcover / landuse maps
2. tree height maps classified short, medium, tall trees
3. elevation maps classified low, medium and high elevation

![ ]({{ site.baseurl }}/images/rfigs/00-Raster-Structure/classified-elevation-map-1.png) 


#What is a geotiff??
I think we need to introduce this format here or somewhere??

##About

In this lesson we will learn how to open a raster in r. We will also learn about 
the key metadata associated with rasters! We will use the `raster` and `rgdal`
libraries.



    library(raster)
    library(rgdal)

##Open up a raster in R

We can use the `raster` function to open a raster in R. NOTE: make sure that your 
working directory is set to the location where the NEON_RemoteSensing directory is 
located on your computer! You can use the `setwd()` to set this.


** it might be worth creating a supplementary lesson on working directories in r**


    # Load raster into r
    DSM <- raster("NEON_RemoteSensing/HARV/DSM/HARV_dsmCrop.tif")
    
    # Look at raster structure
    DSM 

    ## class       : RasterLayer 
    ## dimensions  : 1367, 1697, 2319799  (nrow, ncol, ncell)
    ## resolution  : 1, 1  (x, y)
    ## extent      : 731453, 733150, 4712471, 4713838  (xmin, xmax, ymin, ymax)
    ## coord. ref. : +proj=utm +zone=18 +datum=WGS84 +units=m +no_defs +ellps=WGS84 +towgs84=0,0,0 
    ## data source : /Users/lwasser/Documents/data/1_DataPortal_Workshop/1_WorkshopData/NEON_RemoteSensing/HARV/DSM/HARV_dsmCrop.tif 
    ## names       : HARV_dsmCrop 
    ## values      : 305.07, 416.07  (min, max)

    #quickly plot the raster
    plot(DSM, main="NEON Digital Surface Model\nHarvard Forest")

![ ]({{ site.baseurl }}/images/rfigs/00-Raster-Structure/open-raster-1.png) 

There are several key metadata components that we need to examine when we work with 
rasters. We can see most of this information when we type the name of the raster
and hit return in R studio as we did above. Let's example the metadata more closely.

## Resolution

A raster will have horizontal (x and y) resolution. This resolution
represents the area on the ground that each pixel covers. We will need to know the
units used to calculate resolution as well. In the case of the NEON rasters, the units
are in meters. We can find this using two methods: we can look at the `CRS` string
which we will example in just a bit. or we can use the `DSM@data@unit` to view
the units stored in the geotiff.

#NOTE: the units aren't in the geotiff - look into whether we can add this or 
this is another R bug!


    #view resolution units
    DSM@data@unit

    ## [1] ""
## Coordinate Reference System

The `Coordinate Reference System` or `CRS` is another important raster attribute.
The `CRS` tells R where the raster is located in geographic space. It also tells 
R what method should be used to "flatten" the raster. <<SOME LINK TO MORE ABOUT CRS>>

The CRS in this case is in a `PROJ 4` format. This meanst hat the projection information
is strung together as a series of text elements, each of which begins with a `+`
sign. 
We'll focus on the first few components of the CRS in this lesson.

* `+proj=utm` The projection of this dataset is UTM 
* `+zone=18` If you read more about UTM, you will learn that the UTM projection 
divides up the world into zones. this data happens to be in zone 18 (Massachusettes).
* `+datum=WGS84` The datum tells us how the center point was defined to flatten the data
more on that HERE <<ADD LINK>>
* `+units=m` This is the horizontal units that the data are in. Here we see the units 
are meters. This means that if the data are 1x1 resolution, that each pixel represents
a 1 x 1 meter area on the ground.

image<UTM zone image from other lesson>


    #view raster crs - in Proj 4 format
    crs(DSM)

    ## CRS arguments:
    ##  +proj=utm +zone=18 +datum=WGS84 +units=m +no_defs +ellps=WGS84
    ## +towgs84=0,0,0


#Calculate the Min and Max values for the raster

Sometimes the raster statistics including the min and max values have already been
calculated for us. Other times they have not. If they are not calculated you can
for R to calculate them using the `setMinMax` function. Once they are calculated 
you can then call them using the same `@data` syntax that we used above to look
at the units.


    DSM <- setMinMax(DSM) #This step might be unnecessary
    
    #view min value
    DSM@data@min

    ## [1] 305.07

    #view max value
    DSM@data@max

    ## [1] 416.07

##No Data Values in Rasters

There are two types of "bad" data values to be aware of when working with raster
data.

1. **NoData values:** These are values where no data were collected. The shape of
a raster, by default is always square or rectangular. Thus, if we have a dataset 
that has a shape that isn't square or rectangular, some pixels wil have no data.
This often happens when the data were collected by an airplane which only flew some
of a particular region

<<IMAGE SHOWING NO DATA VALUES>>

If we are lucky, our geotiff file has a tag that tells us what the NoDATA value is.
If we are less lucky, we can find that information in the raster's `metadata`. 
in R, we can use the `NAvalues` function to see if the NoDATA attribute has been
assigned. NOTE: `-INF` is a default value so if you see that - double check the 
metadata!

#Add no data values to geotiff, then add section looking for -9999 values and
#changing to NaN (or equivalent); there would be a challenge here, because the
#-9999 should show up as the min value


    #view raster no data value
    NAvalue(DSM)

    ## [1] -Inf

## Bad data values

Bad data values are different from NoData values. Sometimes a raster's metadata
will tell us a value range of values for the raster. Sometimes, we need to use
some practial scientific insight as we examine the data - just as we would for field
data. For instance, in a mediterranean climate, we would not expect to see 70m 
tall trees! 

<<Insert Image from Victoria's tower discovery at SJER>>

One way to explore the range of values in our raster data is to look at a histogram.
<<This might be covered in lesson 2??>>


    #view raster no data value
    hist(DSM)

![ ]({{ site.baseurl }}/images/rfigs/00-Raster-Structure/view-raster-histogram-1.png) 

## Raster Bands? ?? -- something about that here??

It is important to note that rasters can be either single or multi-band. When data
are brought in using the `raster` function, then we are only bring in one band. 

~(Image on what bands are...)[]


#Challenge

???