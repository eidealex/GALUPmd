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



## 1. **DBSCAN clustering**
For **Defined distance (DBSCAN)**, if the **Minimum Features per Cluster** can be found within the 
**Search Distance** from a particular point, that point will be marked as a core-point and included 
in a cluster, along with all points within the core-distance. A border-point is a point that is 
within the search distance of a core-point but does not itself have the minimum number of features 
within the search distance. Each resulting cluster is composed of core-points and border-points, 
where core-points tend to fall in the middle of the cluster and border-points fall on the exterior. 
If a point does not have the minimum number of features within the search distance and is not a 
within the search distance of another core-point (in other words, it is neither a core-point nor 
a border-point), it is marked as a noise point and not included in any cluster.

![DBSCAN illustration](./pictures/DBSCANex.png)

### 1.1 Usage
This tool is used to detect areas where points are concentrated and where they are separated by
areas that are empty or sparse. Points that are not part of a cluster are labeled as noise.
The results from running the DBSCAN algorithm help us identify points that make up a cluster
and points that are noise.

### 1.2 Example
As an example, we will use **DBSCAN clustering** to identify clusters of gas stations within
Ghana. The datasets used are listed below:

| ID | File Name       | Data Format | Type  | Description                     |
|----|-----------------|-------------|-------|---------------------------------|
| 1  | gas_station.shp | vector      | point | Gas station locations in Ghana  |

The two figures below display the parameter settings and the output of the tool.

| Parameter Settings       | Output      |
| ------------------------ | ----------- |
| ![DBSCAN parameters](./pictures/DBSCANpmtrs.jpg)| ![Ghana Gas stations clusters](./pictures/GhanaGS_clus.png)|



## 2 **Proximity**
The **Proximity** tool calculates the Euclidean distance from the center of the source cell 
to the center of each of the surrounding cells. Conceptually, the Euclidean algorithm works 
as follows: for each cell, the distance to each source cell is determined by calculating 
the hypotenuse with x_max and y_max as the other two legs of the triangle. This calculation
derives the true Euclidean distance, rather than the cell distance. The shortest distance 
to a source is determined, and if it is less than the specified maximum distance, the 
value is assigned to the cell location on the output raster.


![Euclidean distance using trig](./pictures/EucDistEx2.gif)


### 2.1 Usage
This tool gives the measured Euclidean distance from every cell to the nearest source.
The distances are measured in the projection units of the raster, such as feet or meters,
and are measured from cell center to cell center.

>:pushpin: The projected units of a raster layer can be found under the _information_ tab
> in the layer properties

### 2.2 Example
Now we will use the **Proximity** tool in order to determine the Euclidean distance from 
a gas station cluster to the rest of the raster cells.

| ID | File Name      | Data Format | Description                       |
|----|----------------|-------------|-----------------------------------|
| 1  | r_cluster1.tif | raster      | Euclidean distance from cluster 1 |

The two figures below illustrate the parameters and output of the **Proximity** tool.

| Parameter Settings       | Output      |
| ------------------------ | ----------- |
| ![Proximity parameters](./pictures/ProximityPmtrs.jpg)| ![Cluster 1 distance](./pictures/ProximityRasEx.jpg)|

## 3 **Raster Calculator**
- The weighting formula that we will use is: 1/W*Cluster
- W = # of points in a cluster / # of total points
- The most widely used distance decay models are those in which distance is introduced as an inverse function to some power, typically 1 or 2. Thus the value of some variable of interest, z, at location j, zj, might be modeled as some function, f(), of attribute values, zi, associated with other locations, i, weighted by the inverse of the distance separating locations i and j, dij raised to a power, β:
  
## 4 **Reclassify by Table** 
is a tool that assigns new values to a raster band based on a user specified table.

## 5 **r.cross** 
is a tool that takes the cross product of multiple raster category values.

## 6 **subdivide** 
is a tool that divides input geometry into smaller parts, based on the maximum number of nodes specified by the user. 


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


## Assignment/Result
Create IDU for a different district based on the process detailed above