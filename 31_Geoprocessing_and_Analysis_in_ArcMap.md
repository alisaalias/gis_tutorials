
## Premise

We are interested at looking at access to daycare centers accross NYC. First, we will determine the percent of daycare centers in NYC that are in areas with high concentrations of children under 5. Then, we will evaluate which daycare centers are farthest from 
public transportation (subway stations). Finally, we want to find out how many children under 5 are served by daycare centers that are "far" away from subway stations.

## Deliverable

A map showing a buffer around daycares that are 1/4 miles or more away from a subway station.

Answer the following two questions:

What percent of daycare centers are located in areas (i.e. tracts) with high concentrations of children under 5?
What percent of daycare centers in NYC are located farther than 1/4 mile away from a subway station? 
How many children under 5, total, do those "far" daycare centers serve?

## Before beginning

Download the data (same as previous week's lab except Day Care Centers is a shapefile of the daycare centers as points, and adding subway stations layer):

* [Day Care Centers](https://github.com/alisaalias/gis_tutorials/blob/alisaalias-patch-1/data/Day%20Care%20Center.zip)
      **NOTE**: You will have to rename the layer "DaycareCenters" in the Catalog window once you open ArcMap.
* [NYC_TRACTS_2010](https://github.com/alisaalias/gis_tutorials/blob/alisaalias-patch-1/data/2010Tracts-20191025T194412Z-001.zip). 
      **NOTE**: You will have to rename the layer in the Catalog window once you open ArcMap.
* [UnderFiveByTract](https://github.com/alisaalias/gis_tutorials/blob/alisaalias-patch-1/data/UnderFivePopulation.xls). **NOTE**: You may have to save this file as a CSV after downloading it.
* [Subway Stations] (https://github.com/alisaalias/gis_tutorials/blob/alisaalias-patch-1/data/Subway%20Stations.zip)
**NOTE**: You will have to rename the layer SubwayStations in the Catalog window once you open ArcMap.

Add the three shapefiles and the csv to the data frame/table of contents.

**Join** the `UnderFivePopulation` csv to the `TRACTS` shapefile.

**Note:** Save your map project (as a .mxd) periodically.

# Finding daycares near concentrations of children under 5

How many daycares (or what percent of the total number of daycares) are located within census tracts where more than 15.5% of the population
are children under five years old?

* Navigate to menu `Selection` > `Select by Attributes`. Make sure the `TRACTS` layer is the active selection and the selection method is set to `Create a New Selection`. **Double-click** `UFiveP`, click `>`, and type `15.5`. Your expression should look like this:
`"UFiveP" > 15.5`. Click `OK`. In the bottom left-hand corner of the ArcMap window, you should see how many features were selected.

* Right-click on the TRACTS layer and choose `Selection` > `Create Layer from Selected Features`. A new layer will appear on top. Save (`Export`) that new layer to your working directory as a shapefile called `NYC_tracts_kids`. Clear selection by navigating to menu item `Selection` > `Clear selected feautres`. Remove the duplicate TRACTS selection layer. Resymbolize the NYC_tracts_kids layer so that you can see it clearly over the original TRACTS layer. Arrange the layers so that subway stations and daycares are the top two layers.

* We will determine which daycare centers lie within these census tracts by using the select by location tool. Navigate to menu item `Selection` > `Select by location...`. Set the target layer to `DaycareCenters` and the source layer to `NYC_tracts_kids`. Set the selection methods for target layer features to `are within the source layer feature` and click `OK`.

* Open the attribute table of DaycareCenters to see how many day care centers were selected. What percent of the total number of daycare centers fall within these tracts (divide the selected number by the total and multiply that number by 100)?

**Save** your map project.

Now we will turn to the questions regarding the relationship between subway stations and daycare centers in NYC. First, we will address the following question: How many daycare centers are located farther than 1/4 mile away from a subway station? Where are those centers (borough, neighborhood)?

To answer this question, we will create a 1/4 mile buffer around daycare centers. We will be saving a number of new layers so make
sure to save everything in the same folder in your working directory.

## Creating buffers

* Navigate to menu item `Selection` > `Clear Selected Features` to deselect the daycare centers.
* Navigate to menu item `Geoprocessing` > `Buffer`.
* Set Input Features to `SubwayStations` - this sets which layer the buffers will draw around.
* Set the Output Feature Class to `Subways_QuarterMiBuffer.shp` within your working directory.
* Set the buffer distance to 0.25 and set the unit to miles.
* Click `OK`. 

Next we will use the select by location tool to determine which daycare centers fall within ¼ mile of a subway station. Navigate to menu item `Selection` > `Select by location...`.

* Set Target Layer to DaycareCenters and Source Layer to Subways_QuarterMiBuffer. Keep the selection method for target layer features to interect the source layer feature.
* Click `OK`.
* Open the attribute table of DaycareCenters. The selections show how many daycare centers are WITHIN a 1/4 mile of a subway station.
In order to determine how many fall outside the 1/4 distance, subtract the number of selections from the total number of daycare centers. Convert that number into a percent to answer Question #2.

## Spatial Join and Making Estimates

In order to answer our final question regarding the percent of children under five served by daycare centers "far" away from subway stations, we will need to perform a spatial join.

* Deselect the daycare centers by navigating to `Selection` > `Clear Selected Features`. Right-click on `Subways_QuarterMiBuffer` and choose Joins and Relates > Join....
* Uncheck the box next to the Subways_QuarterMiBuffer layer so that it is not visible on the map.
* Create a quarter mile buffer around the daycare centers (`Geoprocessing` > `Buffer', input features are daycare centers, save the output feature class as Daycares_QuarterMiBuffer, set the distance to 0.25 Miles and click `OK`).
* Right-click on the Daycares_QuarterMiBuffer layer and select `Joins and Relates` > `Join...`
* Choose `Join data from another layer based on a spatial location` from the drop-down menu.
* Choose SubwayStations as the layer to join to this layer.
* Since we just want to know which daycare centers are farther than 1/4 mile from a subway station, we don't necessarily need to compute 
a summary, so don't check any of those boxes.
* Save the output shapefile in your working directory as `Daycares_QuarterMiBuffer_SubwaysJoin`
* Click `OK`.

Right-click on the new layer, `Daycares_QuarterMiBuffer_SubwaysJoin`, and open the attribute table. The `count` field tells us how many subway stations are within the 1/4 buffer of each daycare center. Select the features in this layer that have a 0 as their record in the `count` field. Add the selected features to the map as a new layer. Save/export that layer as a shapefile called `Daycares_NoSubways` and add it to the map. Remove the selections copy of `Daycares_QuarterMiBuffer_SubwaysJoin` and clear selections.

How many children under 5 are served by these daycare centers (i.e. how many children under 5 live near these daycare centers)?

We will estimate the population of children under 5 that lives precisely within our ¼ mile buffers using
a method called proportional split estimation. A proportional split is a way to estimate the proportion of a 
quantitative attribute that falls within a portion of a polygon’s area. A proportional split is calculated in a few fairly simple steps.

We calculate the area of each polygon unit:
* Clip the polygons to the boundary of the study area (in our case the ¼ mile buffers)
* Calculate the area of the polygons after clipping them to the study area
* Divide the area of the polygons within the study area by their original area to determine the proportion of the original area that falls within the study area
* Multiply the attributes (for us, population of children under 5) we wish to estimate by the proportion in order to estimate the proportion of the attribute that falls within the study area. 
Note: that proportional split estimation assumes that the attribute you are estimating is evenly distributed through out the polygon. 
In reality the population within each census tract is not evenly distributed nevertheless thus this is an estimate.

## Calculating the area of the census tracts

* Open the attribute table for the TRACTS layer and click the “Table Options” icon and choose Add Field. Name the field Area, of type Double. Click OK.
* Right-Click on the Area field and choose Calculate Geometry. Ignore warnings. Set the Property to Area and click OK.

## Clipping the census tracts to the ¼ mile `Daycares_QuarterMiBuffer_SubwaysJoin` buffers

* Next we will use the clip tool to clip the tracts with the ¼ mile buffers around the Daycare centers that are not near a subway station
* Navigate to `Geoprocessing` > `Clip`.
* Set the Input Features to TRACTS.
* Set the Clip Features to Daycares_QuarterMiBuffer_SubwaysJoin.
* Save the Output Feature Class as Tracts_Daycares_NoSubways_Clip.shp. Click OK.

A new layer containing the census tracts clipped to the ¼ mile buffers around the daycares was added to your map.
Toggle the visibility of all of the layers on your map off except for Tracts_Daycares_NoSubways_Clip.

## Calculating the area of the clipped census tracts

* Now we will calculate the area of these new polygons.
* Open the attribute table for the clipped census tracts layer: `Tracts_Daycares_NoSubways_Clip`
* Create a new field again to calculate the new area of each polygon.
* Name the field `AreaClip`, of type Double. Click `OK`.
* Notice the new field that has been added to the far right of the attribute table called `AreaClip`. 
Right-Click on `AreaClip` and choose `Calculate Geometry`. Set the Property to `Area` and click `OK`.

## Dividing the area of the clipped census tracts by their original area

* With attribute table still open, add a new field.
* Name the field `Proportion`, of type Double.
* Right-Click on the new field `Proportion` and choose `Field Calculator....` Ignore warnings.
* Double-click `AreaClip`, Click `/` sign, and double-click `TRACTS_Area`. Your calculation should look like this: `[AreaClip]/[Area]` -- i.e. the proportion of the original area that remained after the clip.
Click OK

## Multiplying the population by the proportion

* Now we will calculate one final field where we’ll multiply the attributes in order to estimate the proportion of the attribute that falls within the study area.
* Add new field and name it `UnderFive_cl` of type Double.
* Right-Click on the new field `UnderFive_cl` and choose `Field Calculator....` Ignore warnings.
* Double-click `Proportion`, click `*` sign, and double-click `TotUnderFive`. Your calculation should look like this: `[Proportion] * [TotUnderFive]`.

Click OK
Close the attribute table.
Now we will compare the total estimated population within the buffers to the original rough population estimate we made at the beginning of this exercise using the summary statistics tool

Navigate to Geoprocessing > ArcToolbox > Analysis Tools > Statistics > Summary Statistics. 
Set the Input Table to `Tracts_Daycares_NoSubways_Clip`. Save the Output Table to `Tracts_cl_Stats`. 
Set the Statistics Field to TotUnderFive_cl and Statistics Type to SUM. Click OK.

Open the new table to get the estimated total number of children under 5 in the clipped areas (i.e. the areas where there are
daycare centers that are all more than 1/4 mile away from a subway station).