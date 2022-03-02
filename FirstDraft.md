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

# First draft workflow for IDU development in QGIS

## Alex's general conventions:
**GIS Tools**  
`raster and polygon layers`  

## Section 2  
### Summarize key functions

**DBSCAN clustering**
- The example that we are going to use is of Gas stations in Ghana. The DBSCAN clustering tool can be used to identify clusters of gas stations.

**Proximity and Raster Calculator**
- Euclidean Distance of each of the clusters. In order to create a an area and strength of influence for each cluster, we can use the raster calculator to add the rasters together and assign them weights.
- The weighting formula that we will use is: 1/W*Cluster
- W = # of points in a cluster / # of total points

**Reclassify by Table**

**cross**

**subdivide**


`iSDA_MGRS.tif` as input for **Raster pixels to points** output is `Vector points`  
`Vector points` as input for **Extract by Attribute**
- **Extract by Attribute** where the vector points value is equal to '6'
- `Extracted (attribute)` is the output here

`Extracted (attribute` as input for **DBSCAN Clustering** output is `Clusters`
- The parameters for the **DBSCAN Clustering** are: 25 for minimum cluster size and 500 meters as maximum distance between clustered points  

`Clusters` as input for **Extract by Attribute** output is `Extracted (Attribute)` 
- We are going to be extracting each of the clusters by their cluster ID 
`Extracted (Attribute)` as input for **Rasterize** output is `Rasterized`  

`Rasterized` as input for **Proximity** output is `Distance`
- `Distance` output is 'area of influence'  

Repeat the last three steps until you have created an 'area of influence' for all of your clusters.
Once the proximity maps have been created for each cluster, we are going to weight them and add them all together.