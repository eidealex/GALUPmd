# Integrated Decision Units (IDUs)

## What are IDUs and how are they useful?

Land units are areas with properties that differ sufficiently from those of
other land units to affect their suitability for different land uses.
Although any parcel of land can be considered a land unit, it is more efficient
and meaningful to use parcels that can be adequately described in terms of one
or a combination of land properties.
A land unit should therefore represent an area that is, in terms of
predetermined properties, different from the surrounding land and can be
assumed to have **homogeneous** land properties.

![FAO land evaluation framework](https://www.fao.org/3/U1980E/u1980e09.jpg)

Integrated Decision Units (IDUs) are a set of spatial containers, modeled as a set of polygon-based 
Geographic Information System (GIS) coverages containing spatially explicit depictions of landscape 
attributes and patterns.

IDUs are created based on information from multiple sources, such as parcel-level information, 
floodplain delineations, and soil permeability. By using vector-based GIS including intersection, 
expansion, grouping, etc, we can merge the information together to generate a layer of IDUs.

Since IDUs are created based on traits of a landscape, the area within an IDU is deemed homogeneous, 
so a single uniform decision can be made about that IDU without worrying about conflicts caused by heterogeneity.

### The meaning of a set of spatially explicit polygons to land use planning

IDUs essentially are a set of polygons with definite boundaries.
We assign a specific use for each IDU (based on suitability analysis). This assignment is simply referred to
as a land-use decision. 
Since land-use decisions are intrinsically discrete (two adjacent parcels can have different uses, for example), 
a vector (polygon) representation is more applicable than a raster representation. That's why we need to 
have a system of polygons, such as IDUs, to begin with. This is the first, however, 
critical step to achieving a spatially explicit land-use plan.

## First draft workflow for IDU development in QGIS

## Alex's general conventions:
**GIS Tools**  
`raster and polygon layers`  

## Section 2  
### Summarize key functions

`iSDA_MGRS.tif` as input for **r.to.vect** output is `Vectorized`  
`Vectorized` as input for **Extract by Attribute** output is `Extracted`
- **Extract by Attribute** where `Vectorized` value field is equal to 6  
`Extracted` as input for **v.to.rast** output is `Rasterized`  
`Rasterized` as input for **Raster pixels to points** output is `Vector points`  
`Vector points` as input for **DBSCAN Clustering** output is `Clusters`
- The parameters for the **DBSCAN Clustering** are: 25 for minimum cluster size and 500 meters as maximum distance between clustered points  
`Clusters` as input for **Extract by Attribute** output is `Extracted (Attribute)`  
`Extracted (Attribute)` as input for **Rasterize** output is `Rasterized`  
`Rasterized` as input for **Proximity** output is `Distance`
- `Distance` output is 'area of influence'  
Repeat the last three steps until you have created an 'area of influence' for all of your clusters




The essence of this workflow is to create urban IDUs based on suitability anylysis(?). 
First we need to convert the raster layer into a vector/polygon layer. We do this in order to use the **Extract by Attribute** function to show us what land has been deemed urban. In this case, the number '6' has been designated as urban.
In the GRASS toolbox within QGIS there is a function called **r.to.vect**. This function, as its name implies, converts a raster layer (input) to a vector layer (output). 
Using the **r.to.vect** tool, we are going to convert the `iSDA_MGRS` raster layer to `iSDA_MGRS.v`.

_probably a screenshot/video here_

Next we need to extract the areas that are the urban land use designation - '6'. The tool for this is called **Extract by Attribute** which is located in the deafult QGIS toolbox. 

_probably a screenshot/video here_

After we extract the urabn areas that we want, the next step is to convert those areas to point clusters. Since we cannot convert polygons straight to points, we must convert the `iSDA_MGRS.v` layer back to raster. The tool for this is called **v.to.rast** located in the GRASS toolbox.

_probably a screenshot/video here_

Now that we have extracted the urban areas and converted them back to raster, they can now be converted to points. We do this in order to run a clustering algorithm, **DBSCAN**, to determine where our urban clusters are. The conversion from raster to point is done using the **raster pixels to points** tool located in the default QGIS toolbox.

_probably a screenshot/video here_

Using the points layer that we just created, it's time to calculate where our urban clusters are located. The tool for this task is the **DBSCAN clustering** tool in the default QGIS toolbox. 

_probably a screenshot/video here_
