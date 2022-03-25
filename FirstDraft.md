# Integrated Decision Units (IDUs) for Land Use Suitability Modeling

- [Integrated Decision Units (IDUs) for Land Use Suitability Modeling](#integrated-decision-units-idus-for-land-use-suitability-modeling)
  - [1. Introduction](#1-introduction)
    - [1.1 An Integral Part of Land-Use Planning](#11-an-integral-part-of-land-use-planning)
    - [1.2 Some key concepts](#12-some-key-concepts)
    - [1.3 Implication of IDU](#13-implication-of-idu)
  - [2. Key Functions](#2-key-functions)
    - [2.1 DBSCAN clustering](#21-dbscan-clustering)
    - [2.2 Proximity](#22-proximity)
    - [2.3 Raster Calculator](#23-raster-calculator)
    - [2.4 Reclassify by Table](#24-reclassify-by-table)
    - [2.5 Subdivide](#25-subdivide)
  - [3. IDU Workflow](#3-idu-workflow)
  - [Exercise and Post-training Survey](#exercise-and-post-training-survey)
  - [Reference](#reference)

## 1. Introduction

This workshop is the third of a series workshops offered by the Ghana Land Use
Project (GALUP).
As suggested by the title, the workshop deals with creating Integrated Decision
Units (**IDUs**), a vector geographic data represented in polygons.
Two major applications of IDU includes:

- demarcation of a tract of land into smaller (homogeneous) parcels
- creation of (mapping) units for geospatial analysis

We introduce a technical solution for creating IDUs in **QGIS**.
We will begin by introducing the conceptual framework IDU is based on; then,
moving onto QGIS functions that involved in the particular solution; and,
finally, looking at an example of the THLD District in Ghana.

### 1.1 An Integral Part of Land-Use Planning

In 1976, the Food and Agriculture Organization (FAO) of the United Nations
published <a href="#a framework for land evaluation">
<i>A Framework for Land Evaluation</i></a>, in which a system of philosophy and
principles on the evaluation of land potential was developed.
The **_framework_** was tested and adopted in many countries.
In 1990, FAO published another report,
<a href="#land evaluation for development">
<i>Land Evaluation for Development</i></a>,
reiterated the significance of reliable **land evaluation** in the context of
land-use planning and rural development, i.e., developing a rural area into
a denser (urban) area.
In this newer report, land evaluation and its relationship to (i.e., being a
part of) the overall land-use planning process was further elaborated.
As shown in the figure below, land evaluation is a procedure between Stage 2
and Stage 6, as well as a part of the Stage 7.

| ![FAO land evaluation framework](https://www.fao.org/3/U1980E/u1980e01.jpg) |
|:---------------------------------------------------------------------------:|
| Fig 1. The process of land-use planning. _Source_: FAO. (1990).             |

The Stage 4, in parallel with identifying land uses (Stage 3), is identifying
land units, e.g., IDUs.

### 1.2 Some key concepts

> "_**Land units**, or land-mapping units, are areas with and qualities that
> **differ sufficiently** from those of other land units to affect their
> suitability for different land uses._"

- Land Unit: areas of land with specific characteristics (or qualities).
- Land Characteristic: simple attribute that can be measured or estimated. For
  example, _mean annual rainfall_.
- Land Quality: a complex attribute that usually reflects the interaction of
  many land characteristics, such as _susceptibility to flooding_.

Although any parcel of land can be considered a land unit, it is more efficient
and meaningful to use parcels that can be adequately described in terms of one
or a combination of land properties.
A land unit should therefore represent an area that is, in terms of
predetermined properties, different from the surrounding land and can be
assumed to have **homogeneous** land properties.

IDUs essentially are a set of spatial containers, modeled as a set of
polygon-based Geographic Information System (GIS) coverages containing
spatially explicit depictions of landscape attributes and patterns.

IDUs are created based on information from multiple sources, such as
parcel-level information, floodplain delineations, and soil permeability.
By using vector-based GIS including intersection, expansion, grouping, etc., we
can merge the information together to generate a layer of IDUs.

Since IDUs are created based on traits of a landscape, the area within an IDU
is deemed homogeneous, so a single uniform decision can be made about that IDU
without worrying about conflicts caused by heterogeneity.

### 1.3 Implication of IDU

IDUs essentially are a set of polygons with definite boundaries.
We assign a specific use for each IDU (based on suitability analysis).
This assignment is simply referred to as a land-use decision.
Since land-use decisions are intrinsically discrete (two adjacent parcels can
have different uses, for example), a vector (polygon) representation is more
applicable than a raster representation.
That's why we need to have a system of polygons, such as IDUs, to begin with.
This is the first, however, critical step to achieving a spatially explicit
land-use plan.

## 2. Key Functions

### 2.1 DBSCAN clustering
For **Defined distance (DBSCAN)**, if the **Minimum Features per Cluster** can
be found within the **Search Distance** from a particular point, that point 
will be marked as a core-point and included in a cluster, along with all points
within the core-distance. A border-point is a point that is within the search
distance of a core-point but does not itself have the minimum number of 
features within the search distance. Each resulting cluster is composed of 
core-points and border-points, where core-points tend to fall in the middle of 
the cluster and border-points fall on the exterior. If a point does not have 
the minimum number of features within the search distance and is not a within 
the search distance of another core-point (in other words, it is neither a 
core-point nor a border-point), it is marked as a noise point and not included 
in any cluster.

|![DBSCAN illustration](./pictures/DBSCANex.png) |
|:----------------------------------------------:|
|Fig. 2 How DBSCAN is calculated. _Source_: [ESRI.](https://pro.arcgis.com/en/pro-app/latest/tool-reference/spatial-statistics/how-density-based-clustering-works.htm)| 

- **Usage**: This tool is used to detect areas where points are concentrated 
  and where they are separated by areas that are empty or sparse. Points that 
  are not part of a cluster are labeled as noise. The results from running the 
  DBSCAN algorithm help us identify points that make up a cluster and points 
  that are noise.

- **Example**: As an example, we will use **DBSCAN clustering** to identify 
  clusters of gas stations within Ghana. The datasets used are listed below:

| ID | File Name       | Data Format | Type  | Description                    |
|----|-----------------|-------------|-------|--------------------------------|
| 1  | gas_station.shp | vector      | point | Gas station locations in Ghana |

The two figures below display the parameter settings and the output of the tool.

| Parameter Settings       | Output      |
| ------------------------ | ----------- |
| ![DBSCAN parameters](./pictures/DBSCANpmtrs.jpg)| ![Ghana Gas stations clusters](./pictures/GhanaGS_clus.png)|



### 2.2 Proximity
The **Proximity** tool calculates the <u>**Euclidean distance**</u> from the 
center of the source cell to the center of each of the surrounding cells. 
Conceptually, the Euclidean algorithm works as follows: for each cell, the 
distance to each source cell is determined by calculating the hypotenuse with 
x_max and y_max as the other two legs of the triangle. This calculation derives
the true Euclidean distance, rather than the cell distance. The shortest 
distance to a source is determined, and if it is less than the specified 
maximum distance, the value is assigned to the cell location on the output 
raster.


|![Euclidean distance using trig](./pictures/EucDistEx2.gif)|
|:---------------------------------------------------------:|
|Determining true Euclidean distance. _Source_: [ESRI (2021)](https://pro.arcgis.com/en/pro-app/latest/tool-reference/spatial-analyst/understanding-euclidean-distance-analysis.htm)|


- **Usage**:
  This tool gives the measured Euclidean distance from every cell to the 
  nearest source. The distances are measured in the projection units of the 
  raster, such as feet or meters,and are measured from cell center to cell 
  center.

>:pushpin: The projected units of a raster layer can be found under the 
>_information_ tab in the layer properties

- **Example**:
  Now we will use the **Proximity** tool in order to determine the Euclidean 
  distance from a gas station cluster to the rest of the raster cells.

| ID | File Name      | Data Format | Description                |
|----|----------------|-------------|----------------------------|
| 1  | r_cluster1.tif | raster      | Cluster 1 in raster format |

The two figures below illustrate the parameters and output of the **Proximity**
tool.

| Parameter Settings       | Output      |
| ------------------------ | ----------- |
| ![Proximity parameters](./pictures/ProximityPmtrs.jpg)| ![Cluster 1 distance](./pictures/ProximityRasEx.jpg)|



### 2.3 Raster Calculator
The **Raster calculator** tool allows you to create and execute a Map Algebra 
expression that will output a raster. We will be using a distance decay model 
in order to assign weights to each cluster. Distance decay, also known as the 
Gravity Model or the Inverse Square Law, is the tendency of a spatial 
relationship between one place and another to weaken as the distance between 
them increases.

- **Usage**:
  The **Raster calculator** tool is used to individually weight and multiply 
  proximity rasters together.
- The weighting formula that we will use is: 1/W*Cluster
- W = # of points in a cluster / # of total points


  **Example**:

| ID | File Name            | Data Format | Description                       |
|----|----------------------|-------------|-----------------------------------|
| 1  | cluster1_euc_dst.tif | raster      | Euclidean distance from cluster 1 |
| 2  | cluster2_euc_dst.tif | raster      | Euclidean distance from cluster 2 |

The figures below show the parameters and output of the **Raster calculator** 
tool.

| Parameter Settings       | Output      |
| ------------------------ | ----------- |
| ![Raster calculator parameters](./pictures/RastCalcPrmtrs.jpg)| ![Raster Calculator output](./pictures/RastCalcEx.jpg)|



### 2.4 Reclassify by Table 
**Reclassify by table** is a tool that reclassifies a raster band by assigning 
new class values based on ranges specified in a fixed table.

- **Usage**:
  This tool is used to reclassify raster values. 

- **Example**:

| ID | File Name              | Data Format | Description                                            |
|----|------------------------|-------------|--------------------------------------------------------|
| 1  | euc_cluster_output.tif | raster      | Result of adding together the weighted clusters 1 and 2|

The figures below show the parameters and output for the 
**Reclassify by table** tool.

> :pushpin: Check the details of an image:  
> If you can't see the image clearly, click on the image to view it in a
> new page, which will show the image in its original size.

| Parameter Settings       | Table parameters      | Output|
| ------------------------ | ----------- |--------|
| ![Reclassify parameters](./pictures/RclsfyP1.jpg) | ![Reclassify table parameters](./pictures/RclsfyP2.jpg)|![Raster Calculator output](./pictures/RclsfyEx.jpg)|


### 2.5 Subdivide 
is a tool that subdivides the original geometry into smaller parts, where no 
part has more than the specified maximum number of nodes. 

- **Usage**
  The **subdivide** tool is used to break down complex geometries into more 
  manageable parts. In our case, this tool allows us to subdivide large areas 
  of land into IDU's.

- **Example**:

| ID | File Name        | Data Format |Type    | Description                     |
|----|------------------|-------------|--------|---------------------------------|
| 1  | subdivide_ex.shp | vector      |polygon | Geographic area to be subdivided|

The figures below show the parameters and output for the **Subdivide** tool.

|Input | Parameter Settings       | Output      |
|------| ------------------------ | ----------- |
|![Subdivide Input](./pictures/SubDivBaseEx.jpg)| ![Raster calculator parameters](./pictures/SubDivParameters.jpg)| ![Subdivide output](./pictures/SubDivEx.jpg)|

## 3. IDU Workflow

The diagrams below show the general process of the IDU workflow.
|Developing urban clusters                           |
|----------------------------------------------------|
|![IDU Workflow Part 1](./pictures/IDU_diagram_1.svg)|

|Calculating IDU's                                   |
|----------------------------------------------------|
|![IDU Workflow Part 2](./pictures/IDU_diagram_2.svg)|

>:pushpin: When selecting small polygons to eliminate, an area column must be
>calculated for for the rural and urban vector files. This can be done by 
>opening the layer's attribute table and calculating a new field based on the 
>'$area'.

>:pushpin: If you are having trouble permanently saving your subdivided
>polygons, delete the 'fid' from the FID row under the 'layer options' of 
>the 'Save Feature Layer as...'

|Final IDU Map                                   |
|------------------------------------------------|
|![IDU Workflow Part 1](./pictures/final_IDU.png)|


## Exercise and Post-training Survey
* Please complete the Exercise 1.
* Please take this post-training survey (required).

## Reference

1. <a id="a framework for land evaluation"></a>FAO. (1976). A framework for land evaluation (No. 32; Soils Bulletins). Food and Agriculture Organization of the United Nations. http://www.fao.org/3/X5310E/x5310e00.htm
2. <a id="land evaluation for development"></a>FAO. (1990). Land evaluation for development. Food and Agriculture Organization of the United Nations. https://www.fao.org/3/U1980E/u1980e00.htm
