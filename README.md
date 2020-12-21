# Johns Hopkins University Geoportal Metadata Application Profile (JHU-GMAP) 1.0

## Introduction

In 2020, Data Services at Johns Hopkins University instituted a geoportal based on the  platform to better connect users to our collections of geospatial data and historic maps. The Johns Hopkins University Geoportal Metadata Application Profile (JHU-GMAP) described in this document establishes best practices for metadata creation for Data Services’ geoportal. By promoting consistent and standardized metadata, we hope to improve the searchability, browsability, and findability of geoportal resources for our users.

Our metadata profile is based on the [GeoBlacklight (GBL) Metadata Schema Version 1.0](https://github.com/geoblacklight/geoblacklight/wiki/GeoBlacklight-Metadata), which was made to help standardize metadata across GeoBlacklight instances. A “metadata schema for GIS resource discovery” based on Dublin Core, the GBL schema focuses on descriptive elements meant to aid user discovery rather than technical metadata. JHU-GMAP shares this  descriptive focus. JHU-GMAP also draws from the [Big Ten Academic Alliance Geospatial Data Project’s Metadata Profile (version 3.1)](https://sites.google.com/umn.edu/btaa-gdp/about/project-documents/metadata-guides?authuser=0), which is also based on the GBL schema.

Under the GBL schema, metadata is encoded and submitted to GeoBlacklight instances as JSON files. While our metadata will also be submitted as JSON files, this guide is more focused on how to properly understand and transcribe individual elements than how to encode them in JSON. More information on transforming this metadata from a CSV to a JSON file can be found: here.

For those familiar with the GBL schema, there are some key differences between the GBL schema and JHU-GMAP that may be useful to note.

- The GBL schema requires the use of only 6 elements; JHU-GMAP requires a total of 14 elements (the 6 required by GBL and 8 additional elements). Therefore, metadata created under JHU-GMAP requirements always will fulfill GBL schema requirements, but the reverse is not necessarily true.

- For the elements of [Creator](#Creator), [Publisher](#Publisher), [Subjects](#Subject), and Spatial Coverage, JHU-GMAP requires the use of selected controlled vocabularies or adherence to specific formatting rules. The GBL schema recommends but does not require controlled vocabularies for these elements.

### A note on examples:

In the examples in this guide, the examples are shown how they would be entered in a spreadsheet for subsequent conversion to JSON through a script. This means that elements with multiple values are shown with each value separated by a pipe, which will later be converted into an array value in JSON. For instance, see the following Spatial Coverage element with multiple values as entered in a spreadsheet and as seen in the final JSON document.

***Element and values in spreadsheet:***

| Spatial Coverage                                             |
| ------------------------------------------------------------ |
| District 9, Franklin, Maryland, United States\|Hampstead, Maryland, United States\|New Windsor, Maryland, United States\|Winfield, Maryland, United States |

***Element and values in JSON file:***

```json
"dct_spatial_sm": [
    "District 9, Franklin, Maryland, United States",
    "Hampstead, Maryland, United States",
    "New Windsor, Maryland, United States",
    "Winfield, Maryland, United States"
  ]
```

Some examples in JHU-GMAP are drawn from items in the Big Ten Academic Alliance Geoportal.

For questions, help, or to report an error in this profile, please contact Data Services at dataservices@jhu.edu.

## Summary Chart

| **Field name***   | URI                   | Required | Repeatable | Type      | Controlled Vocabulary                                        |
| ----------------- | --------------------- | -------- | ---------- | --------- | ------------------------------------------------------------ |
| [Bounding Box](#Bounding-Box)      | solr_geom             | x        |            | String    |                                                              |
| [Description](#Description)       | dc_description_s      | x         |           | String              
| [Format](#Format)            | dc_format_s           | x        |            | String    | [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values) |
| [Geometry Type](#Geometry-Type)     | layer_geom_type_s     | x        |            | String    | [Geometry Type Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Geometry-type-values) |
| Identifier        | dc_identifier_s       | x        |            | String    |                                                              |
| [Provenance](#Provenance)        | dct_provenance_s      | x        |            | String    |                                                              |
| Rights            | dc_rights_s           | x        |            | String    | "Public" or "Restricted"                                     |
| Schema Version    | geoblacklight_version | x        |            | String    | "1.0"                                                        |
| [Slug](#Slug)             | layer_slug_s          | x        |            | String    |                                                              |
| [Spatial Coverage](#Spatial-Coverage)  | dct_spatial_sm        | x        | x          | Array     | [GeoNames](https://www.geonames.org/)                        |
| [Subject](#Subject)           | dc_subject_sm         | x        | x          | Array     | [ISO Topic Categories](https://airtable.com/shrAftLyILyjheDZx); [Library of Congress Subject Headings](https://airtable.com/shrwU7SjndL5chKcs)   |
| [Title](#Title)             | dc_title_s            | x        |            | String    |                                                              |
| [Type](#type)              | dc_type_s             | x        | x          | String    | [GeoBlacklight Type Values](https://github.com/geoblacklight/geoblacklight/wiki/Type-values) |
| [Creator](#Creator)           | dc_creator_sm         |          | x          | Array     | [Library of Congress Name Authority File](http://id.loc.gov/authorities/names.html) |
| [Date Issued](#Date-Issued)       | dct_issued_s          |          |            | String    |                                                              |
| Is Part Of        | dct_isPartOf_sm       |          | x          | Array     |                                                              |
| Language          | dc_language_sm        |          | x          | Array     |                                                              |
| [Layer ID](#Layer_ID)          | layer_id_s            |          |            | String    |                                                              |
| [Modified Date](#Modified-Date)     | layer_modified_dt     |          |            | Date-time |                                                              |
| [Publisher](#Publisher)         | dc_publisher_sm       |          | x          | Array     | [Library of Congress Name Authority File](http://id.loc.gov/authorities/names.html) |
| References        | dct_references_s      |          |            | String    |                                                              |
| [Solr Year](#Solr-Year)         | solr_year_i           |          |            | Integer   |                                                              |
| Source            | dc_source_sm          |          | x          | Array     |                                                              |
| Suppressed        | suppressed_b          |          |            | Boolean   |                                                              |
| [Temporal Coverage](#Temporal_Coverage) | dct_temporal_sm       |          | x          | Array     |                                                              |

## GeoBlacklight Fields

### Bounding Box
**URI:** solr_geom<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Only in search map interface; not displayed in item detail page.

**Description:** Also known as the minimum bounding rectangle (MBR), this is the rectangular spatial extent of the resource. It is given in coordinates and is indexed as a Solr spatial (RPT) field to aid searching with the map interface. Note that the bounding box does not indicate exact extents, but is meant to facilitate discovery.

**Controlled Vocabularies:** None

**Instructions:** Record the bounding box of the layer using coordinates in West, South, East, North order, separating each coordinate with commas. The coordinates should be in decimal degrees (DD), not Degrees Decimal Minutes/Seconds.

The [Klokantech Bounding Box](https://boundingbox.klokantech.com/) tool can be used to generate the bounding box. In the map interface, navigate to the general area of interest. Resize the borders of the polygon rectangle on the screen to define the spatial extent for the resource in question. In the lower left-hand corner, change the format on the dropdown menu to CSV, then copy the resulting output extents.

The four coordinates should be provided as decimal degrees, rounded down to the nearest 6 decimal places. Do not go smaller than city level for the bounding box coordinates.

Later, a script will automatically fit the bounding box into the ENVELOPE WKT (from the CQL standard) pattern: ENVELOPE(.,.,.,.). It will also convert the West, South, East, North order of the coordinates to the required order of West, East, North, South.

<details>
<summary><b>Examples</b></summary>

***Example 1:***<br/>
*Title*: Percent Population Under 18, Baltimore, Maryland, 1970
*Spatial Coverage*: Baltimore, Maryland, United States
*Bounding Box*: -76.711298,39.197233,-76.529676,39.371971

***Example 2:***<br/>
*Title*: Carte de la Baie de Chesapeake et de la Partie Navigable des Rivieres, James, York, Patowmack, Patuxen, Patapsco, North-East, Choptank et Pokomack
*Spatial Coverage*: Chesapeake Bay, United States
*Bounding Box*: -77.3929,36.7947,-75.1627,39.634

***Example 3:***<br/>
*Title*: 108th Congressional Districts for the Delaware River Basin Area
*Spatial Coverage*: West Branch Delaware River, Pennsylvania, United States
*Bounding Box*: -80.989450,38.325955,-71.566326,45.022861

***Example 4:***<br/>
*Title*: Plate 1, Montgomery County, 1917
*Spatial Coverage*: Montgomery County, Maryland, United States
*Bounding Box*: -77.284141,39.099501,-77.164321,39.152702
</details>

### Description
**URI:** dc_description_s<br>
**Required:** Yes<br>
**Repeatable:** No<br>
**Type:** String<br>
**Displayed:** Yes

***Definition:*** A description of the resource that may include additional relevant information, like data creation methods, data sources, and special licenses. If the description is minimal or lacking, it can be improved by concatenating available metadata fields, such as title, date, format, and place.

***Instructions:*** Create descriptions as complete sentences, ending with punctuation. Concatenate applicable title components, summary components, and scale components (described below) together to create the description. Expand any abbreviations if possible. If any relevant description(s) or abstract(s) come with the resource, add it to the description as written and include additional information as needed.

If deriving descriptions from MARC records, please use specific instructions found in [Appendix C](#Appendix_C).

*Title components:*
Include the following information if applicable:
  - Formal title and [temporal coverage](#Temporal_Coverage) of the resource.
  - Alternative titles.
  - Edition statement.
  - Series statement.

*Summary components:*<br>
Include the following information if applicable and easily identified:
  - The specific information represented by the resource, including the subjects or categories of the resource.
  - Spatial coverage of the resource.
  - Information on how the resource is organized.
  - Information on frequency of publication, if part of a recurring series.
  - Information about the original data source. May include:
  - Source(s) of original data, including biographical information on relevant organization(s) or agencies.
  - Reason(s) for collection or creation of data.
  - Data creation methods.
  - Data processing.
  - Data definitions.

*Scale components:*<br>
Include the following information if applicable and easily identified:
  - Scale information.
  - Projection information.
  - Resolution information.

***Example 1:***<br>
*Title components:*<br>
Title: MNDNR Administered State Forest Roads<br>
Temporal Coverage: 2014

*Summary components:*
Abstract: Roads administered by the Commissioner of Natural Resources to provide access to lands administered by the Division of Forestry. These roads are generally open to the public for recreational use. Some of the segments in this shapefile are components of County administered forest road systems which cross State land. State Forest Roads are classified as either system roads or minimum maintenance roads. System roads are generally well maintained and capable of supporting low-clearance highway licensed vehicles. Minimum maintenance roads are not capable of sustaining routine traffic by highway licensed vehicles. They are posted with signs which state "Road may be impassable. Travel at your own risk".

Purpose: To provide an inventory of the State Forest Road system for access to perform resource management activities and as a reference for recreational activities.

*Scale components:* N/A

*Description:* Minnesota Department of Natural Resources Administered State Forest Roads, 2014. Created to provide an inventory of the Minnesota State Forest Road system for access to perform resource management activities and as a reference for recreational activities. Roads administered by the Commissioner of Natural Resources to provide access to lands administered by the Division of Forestry. These roads are generally open to the public for recreational use. Some of the segments in this shapefile are components of County administered forest road systems which cross State land. State Forest Roads are classified as either system roads or minimum maintenance roads. System roads are generally well maintained and capable of supporting low-clearance highway licensed vehicles. Minimum maintenance roads are not capable of sustaining routine traffic by highway licensed vehicles. They are posted with signs which state "Road may be impassable. Travel at your own risk".

***Example 2:***
*Title components:* <br>
Title: Abandoned Mine Land Inventory Sites: Pennsylvania, 2019<br>
Edition: Unknown

*Summary components:*
Abstract: This data set portrays the approximate location of Abandoned Mine Land Problem Areas containing public health, safety, and public welfare problems created by past coal mining. It is a subset of data contained in the Office of Surface Mining (OSM) Abandoned Mine Land Inventory. This layer represents the AML Inventory Sites, which are the boundary of an entire problem area. All related AML point/polygon features must fit within the boundary of the designated problem area.

Purpose: This data set provides information needed to implement Title IV Abandoned Mine Reclamation, of the Surface Mining Control and Reclamation Act (SMCRA) of 1977. One of the major uses of this data set is for the reporting of annual Abandoned Mine Land Program accomplishments to Congress. In addition, the data is used in the National Atlas of the United States for geographic display and analysis at the national level, and for large regional areas.
Process_Description: Presently data is collected partly via hand drawn maps that are then converted to a digital format or the data is received from field offices in a shapefile format.

*Scale components:* N/A

*Description:* Abandoned Mine Land Inventory Sites: Pennsylvania, 2019. This data set portrays the approximate location of Abandoned Mine Land Problem Areas containing public health, safety, and public welfare problems created by past coal mining. It is a subset of data contained in the Office of Surface Mining (OSM) Abandoned Mine Land Inventory. This layer represents the AML Inventory Sites, which are the boundary of an entire problem area. All related AML point/polygon features must fit within the boundary of the designated problem area. This data set provides information needed to implement Title IV Abandoned Mine Reclamation, of the Surface Mining Control and Reclamation Act (SMCRA) of 1977. One of the major uses of this data set is for the reporting of annual Abandoned Mine Land Program accomplishments to Congress. In addition, the data is used in the National Atlas of the United States for geographic display and analysis at the national level, and for large regional areas. Presently data is collected partly via hand drawn maps that are then converted to a digital format or the data is received from field offices in a shapefile format.

***Example 3:***<br>
*Title components:*<br>
Title: Wingate Quadrangle Maryland, MGS-01-257-1<br>
Edition: Second Edition <br>
Date of Photography: March 25, 1995<br>
Series statement: 7.5 Minute Series (Orthophotoquad)

*Summary components:*

Base Image: Composite of resampled digital orthophoto quarter quads (Wingate - NE, NW, SE, SW) produced by the Maryland Department of Natural Resources (DNR) from aerial photography flown on March 25, 1995

Shorelines: 1849 - Historical Shorelines CZM Map 73A (Maryland Geological Survey, 1975); digitized using AutoCAD. 1942 - National Oceanic and Atmospheric Administration, National Ocean Service T-sheet listed below; vectorized using CADCORE. T-sheet T-8119. 1995 - Orthophotoquad shoreline extracted from a DNR digital wetlands delineation based on photo interpretation of digital orthophoto quarter quads.

Acknowledgments: This map was produced using the geographic information system TNTmips by Microimages, Inc. Partial funding was provided by a grant from the National Oceanic and Atmospheric Administration (Award No. NA 07OZ0 118), administered by the Maryland Department of Natural Resources, Coastal Zone Management Program (CZM Grant M0l-056 CZM 040).

*Scale components:*
SCALE 1:24000
Projection and 1000-meter grid ticks: Maryland State Plane Coordinate System

*Description:* Wingate Quadrangle Maryland, 1995. MGS-01-257-1. Second Edition. From the 7.5 Minute Series (Orthophotoquad). This map compares shorelines from 1849, 1942, and 1995. The base image is a composite of resampled digital orthophoto quarter quads (Wingate - NE, NW, SE, SW) produced by the Maryland Department of Natural Resources (DNR) from aerial photography flown on March 25, 1995. 1849 shoreline from Historical Shorelines CZM Map 73A (Maryland Geological Survey, 1975); digitized using AutoCAD. 1942 shoreline from National Oceanic and Atmospheric Administration, National Ocean Service T-sheet T-8119; vectorized using CADCORE. 1995 orthophotoquad shoreline extracted from a DNR digital wetlands delineation based on photo interpretation of digital orthophoto quarter quads. This map was produced using the geographic information system TNTmips by Microimages, Inc. Partial funding was provided by a grant from the National Oceanic and Atmospheric Administration (Award No. NA 07OZ0 118), administered by the Maryland Department of Natural Resources, Coastal Zone Management Program (CZM Grant M0l-056 CZM 040). Scale 1:24000. Projection and 1000-meter grid ticks: Maryland State Plane Coordinate System.

### Format
**URI:** dc_format_s<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Yes

**Description:** This indicates the file format of the data. If a download link is included, this value shows up on the item page display in the download widget. This element is required for Download functionality.

**Controlled Vocabularies:** [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values)

**Instructions:** Select the appropriate format type for your file from the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values). Be sure to copy the text string for the format exactly from the vocabulary.

The identified file types can be classified into the following:
* Dataset (including tabular data, as well as various file formats for vector and raster geometry types, often requiring geospatial software to open)
* Image
* Interactive Resource
* Physical Object

<details>
<summary><b>Examples</b></summary>

***Example 1:***<br/>
*File(s)*: MarylandMap.tiff<br/>
*Notes*: This is an image file type. Refer to the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values), Type: Image.<br/>
*Format*: TIFF

***Example 2:***<br/>
*File(s)*: Sheet_1_3N-4W.jpg<br/>
*Notes*: This is an image file type. Refer to the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values), Type: Image.<br/>
*Format*: JPEG

***Example 3:***<br/>
*File(s)*: 31151021522044.jp2<br/>
*Notes*: This is an image file type. Refer to the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values), Type: Image.<br/>
*Format*: JPEG2000

***Example 4:***<br/>
*File(s)*: cd108_polygon.dbf, cd108_polygon.prj, cd108_polygon.sbn, cd108_polygon.sbx, cd108_polygon.shp, cd108_polygon.shp.xml, cd108_polygon.shx<br/>
*Notes*: This is a dataset file type. These files combined make up a single shapefile. Refer to the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values), Type: Dataset.<br/>
*Format*: Shapefile

***Example 5:***<br/>
*File(s)*: Maryland_Physical_Boundaries.gdb<br/>
*Notes*: This is a dataset file type, consisting of a file folder with the '.gdb' extension. Refer to the [GeoBlacklight Format Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Format-values), Type: Dataset.<br/>
*Format*: Geodatabase
</details>

### Geometry Type
**URI:** layer_geom_type_s<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:**

**Description:**
A data type that describes how the resource represents and/or structures its spatial data. This field helps to differentiate between vector (Point, Line, Polygon), raster (Raster, Image), nonspatial formats (table), or a combination (Mixed). The field is tied to icons for the resource, and provides the user with visual clues to the item.

**Controlled Vocabularies:** [Geometry Type Controlled Vocabulary](https://github.com/geoblacklight/geoblacklight/wiki/Geometry-type-values)

**Instructions:**
Examine the resource and choose the corresponding geometry type from the [GeoBlacklight Geometry Type Values](https://github.com/geoblacklight/geoblacklight/wiki/Geometry-type-values). The value selected has an associated icon that is displayed to the user. Be sure to copy the text string for Geometry Type exactly from the vocabulary.

If you are unsure what the correct geometry type is, open the resource in a geospatial desktop software, such as QGIS or ArcGIS Pro for further examination.

Note that multipart geometries, where one feature is made up of multiple simple geometries (examples of multipart geometres are multipoints, multilines, and multipolygons) are not defined as part of the GeoBlacklight Geometry Type Values.   

<details>
<summary><b>Examples</b></summary>

***Example 1:***<br/>
*Title*: Orange County, New York land parcels, 2007<br/>
*Description*: Land parcels in Orange County, New York, 2007.<br/>
*Geometry type*: Polygon

***Example 2:***<br/>
*Title*: LiDAR-Derived 2-foot Contours for Ashland County, WI 2014<br/>
*Description*: This data represents LiDAR-derived 2-foot contours for Ashland County, Wisconsin in 2014. A contour is a line through all contiguous points of equal elevation value. Contours are vector features used to represent the elevation of a landscape in a relatively familiar way. They are typically used for basemaps and general topographic representation.<br/>
*Geometry type*: Line

***Example 3:***<br/>
*Title*: Address Points: Prince George's County, Maryland <br/>
*Description*: This layer contains locational property address identifiers for the numbered thoroughfare addresses within Prince George’s County, Maryland.<br/>
*Geometry type*: Point

***Example 4:***<br/>
*Title*: Insurance maps of Garfield and Wallington, Bergen County, New Jersey<br/>
*Description*: Fire insurance maps by the Sanborn Map Company. Includes key, streets and specials index, and text. "2296." Historic Maps copy includes sheets 1-14, covering Garfield; sheets 15-24, covering Wallington, foldered separately.<br/>
*Geometry type*: Image

***Example 5:***<br/>
*Title*: Annual Precipitation: San Francisco Bay Area, California, 2005<br/>
*Description*: This raster dataset depicts the annual precipitation by averaging monthly precipitation within the nine county San Francisco Bay Area Region, California in 2005. This data was primarily used in the climatic stratification of the annual grasslands for the vegetation dataset. It was also used to report climatic information in the Biodiversity Portfolio Reports in the Conservation Lands Network Explorer. This data comes from the PRISM Climate Group at Oregon State University. This dataset was developed/compiled for use in the San Francisco Bay Area Upland Habitat Goals Project and contributed to the developement of the Conservation Lands Network.<br/>
*Geometry type*: Raster

***Example 6:***<br/>
*Title*: Real Property Taxes, Baltimore City, Maryland<br/>
*Description*: A csv file of 2020 real property tax values in Baltimore city.
*Note*: The dataset includes location information (address and x, y coordinates) but the file format is tabular in nature. This file can be converted to a geospatial datatype using geospatial software, but it is available for download in a non-geospatial data format.
*Geometry type*: Table
</details>

### Provenance
**URI:** dct_provenance_s<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Yes

**Description:** The shortened name of the institution that holds the resource or acts as the custodian for the metadata record. Projects are encouraged to submit their shortened name and institutional icon to the GeoBlacklight Icons repository to display with the resource.

**Controlled Vocabularies:** [GeoBlacklight Icons]https://github.com/geoblacklight/geoblacklight-icons)

**Instructions:** For items held by Johns Hopkins University, use the name “Johns Hopkins” for this element. Otherwise, lookup the shortened name in the GeoBlacklight Icons repository.


### Slug
**URI:** layer_slug_s<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Yes

**Description:** A string appended to the base URL of a GeoBlacklight installation to create a unique landing page for each resource. It is visible to the user and serves the purpose of forming a persistent URL for each catalog item.

**Instructions:** The slug will be auto-generated through the ingest process.

### Spatial Coverage
**URI:** dct_spatial_sm<br/>
**Required:** Yes<br/>
**Repeatable:** Yes<br/>
**Type:** Array<br/>
**Displayed:** Yes

**Description:** Field for place name keywords. Place name strings should be specified out to the nation level, and should represent the largest extent that the data layer represents.

**Controlled Vocabularies:** [GeoNames](https://www.geonames.org/)

**Instructions:** Choose place names from GeoNames that represent the largest extent of the resource. For data layers, select at least one GeoName for each layer that corresponds to the logical extent of the area of that layer. Don’t add additional GeoNames that fall within the area of previously assigned GeoNames unless they are topically relevant to the content of the data. For instance, do not add Baltimore, Maryland, United States if you have already assigned Maryland, United States.

Do not use ending punctuation. Always normalize your place names out to the country level for all terms. See below for examples.

| Geographic level      | Normalized GeoName |
| ------------ | -------------- |
| Neighborhood, city, state, country | Old Goucher, Baltimore, Maryland, United States |
| City, state, country | Dublin, Ohio, United States |
| County, state, country | Cook County, Illinois, United States |
| Feature, state (optional), country | Mississippi River, United States |
| State or territory, country | British Columbia, Canada |
| Country | Maldives |




### Subject

**URI:** dc_subject_sm<br/>
**Required:** Yes<br/>
**Repeatable:** Yes<br/>
**Type:** Array<br/>
**Displayed:** Yes

**Description:** Theme or topic keywords of the resource chosen from a controlled vocabulary.

**Controlled Vocabularies:** [Library of Congress Cartographic Genre](https://airtable.com/shrwU7SjndL5chKcs); [ISO Topic Category](https://airtable.com/shrAftLyILyjheDZx)

**Instructions:** Geospatial data should have at least one [ISO Topic Category](https://airtable.com/shrAftLyILyjheDZx). Maps should have both a [Library of Congress Cartographic Genre](https://airtable.com/shrwU7SjndL5chKcs) (if applicable) and at least one [ISO Topic Category](https://airtable.com/shrAftLyILyjheDZx). Aerial imagery should have the ISO Topic Category of “Imagery and Base Maps”. Do not use Library of Congress Subject Headings to indicate the geography or spatial coverage of a resource. Copy the subject string exactly from its controlled vocabulary; do not alter its capitalization.



<details>
<summary><b>Examples</b></summary>

***Example 1:***<br/>
*Title*: Residential Land Use: Minnesota, 1949 and 1959<br/>
*Description:* Residential Land Use: Minnesota, 1949 and 1959. This dataset outlines areas that had residential land use in 1945 and 1959 as shown on a map included in the Metropolitan Land Study, dated April 1960.<br/>
*Type:* Dataset<br/>
*Subject(s):* Planning and Cadastral<br/>

***Example 2:***<br/>
*Title:* A map of the surface formations of Minnesota, 1914-1916<br/>
*Description:* A map of the surface formations of Minnesota, 1914-1916. 1 map on 3 sheets : color ; on sheets 76 x 96 or smaller. Relief shown by contours on sheets 2 and 3. Scale 1:500,000.<br/>
*Type:* Image<br/>
*Subject(s):* Geological maps|Geoscientific Information

***Example 3:***<br/>
*Title:* Aquatic Life Use Monitoring, Ohio Environmental Protection Agency: Ohio, 2018<br/>
*Description:* Aquatic Life Use Monitoring, Ohio Environmental Protection Agency: Ohio, 2018. These data include sites where sampling and monitoring of aquatic life was conducted, and sites where aquatic life beneficial uses were assessed. The monitoring may include fish and/or invertebrate sampling. For more information see the 2018 Integrated Report Section G on aquatic life use assessment and monitoring.<br/>
*Type:* Dataset<br/>
*Subject(s):* Biota|Environment

***Example 4:***<br/>
*Title:* Zoning plan for City of Kendallville, Indiana, 1948<br/>
*Type:* Image<br/>
*Subjects(s):* Zoning maps|Planning and Cadastral<br/>
</details>

### Title

**URI:** dc_title_s<br/>
**Required:** Yes<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Yes<br/>

**Description:** The name of the resource. Titles should include place names and dates when available.

**Instructions:**

***GIS data***<br/>
When practical, titles for GIS data are changed to the format theme: place. Dates can be added at the end if the dataset is for a specific time period. If applicable, move dates or numbers from the beginning of the title to facilitate browsing in the geoportal. Please use sentence-case capitalization.

***Digitized maps***<br/>
For scanned maps, use their official title from [Catalyst](https://catalyst.library.jhu.edu/) or Horizon (the library catalog) or [WorldCat ](https://www.worldcat.org/)for this element. Include the subtitle (subfield b in field 245). Remove any ending punctuation, including periods and backslashes. Do not include the statement of responsibility (subfield c in field 245). If known, always append date of publication or temporal coverage to the end of the title.

If there is no official name of a scanned map (for instance, a map from an atlas), use the title or heading printed on the map as a starting point. Keep any series numbering associated with the title. If the title does not include enough information to make the geographic location of the map apparent, append additional location or series information to the title. If known, always append date of publication or temporal coverage to the end of the title. Spell out any abbreviations. Do not add ending punctuation. Please use sentence-case capitalization.

***Example 1:***<br/>
*Printed on map:* CARTE PARTICULIERE DE VIRGINIE, MARYLAND, PENNSILVANIE. La Nouvelle Iarsey Orient et Occidentale.<br/>
*Title in MARC:* 245 0 0 a| Carte particuliere de Virginie, Maryland, Pennsilvanie, la Nouvelle Iarsey orient et occidentale.<br/>
*Date issued:* 1696<br/>
*Title:* Carte particuliere de Virginie, Maryland, Pennsilvanie, la Nouvelle Iarsey orient et occidentale, 1696

***Example 2:***<br/>
*Printed on map sheet in atlas:* Mt. Washington<br/>
*Title of atlas (parent record):* Baltimore City Atlas, 1902<br/>
*Date Issued:* 1902*Title:* Mt. Washington, Baltimore, 1902

***Example 3:***<br/>
*Printed on map sheet in atlas:* Hydrogeologic Atlas, Hampstead Quadrangle, Baltimore and Carroll Counties, Maryland; Map 5. Geohydrologic Constraints on Septic Systems<br/>
*Title of atlas (parent record):* Quadrangle Atlas No. 12 Hampstead Quadrangle: Hydrogeology<br/>
*Date Issued:* 1981<br/>
*Title:* Map 5: Geohydrologic constraints on septic systems, Hampstead Quadrangle, Baltimore and Carroll Counties, Maryland, 1981

***Example 4:***<br/>
*Printed on map sheet in atlas:* Map 5-1 Sampling Locations by Robert H. Cuthbertson and Jeffrey P. Halka; Sulfur Content by Lamere Hennessee and Jeffrey P. Halka 1982; United States--East Coast, Maryland, Chesapeake Bay, Patuxent River and Vicinity<br/>
*Title of atlas (parent record):*  Chesapeake Bay earth science atlas no. 5: Patuxent River and vicinity<br/>
*Date Issued:* 1982<br/>
*Title:* Map 5-1: Sampling locations and sulfur content, Patuxent River and vicinity, Maryland, 1982


## Type

**URI:** dc_type_s<br>
**Required:** Yes<br>
**Repeatable:** Yes<br>
**Type:** String<br>
**Displayed:** Yes

**Description:** A descriptive word that indicates the larger genre of the resource. This element is useful for categorizing between datasets, scanned maps, and collections.

Note: to match the practices of the [Big10 Academic Alliance Geoportal](https://geo.btaa.org/), we are making this field repeatable.

**Controlled Vocabulary:** [GeoBlacklight Type Values](https://github.com/geoblacklight/geoblacklight/wiki/Type-values)


**Instructions:** The GeoBlacklight schema observes the Dublin Core controlled vocabulary for [Type](https://github.com/geoblacklight/geoblacklight/wiki/Type-values). Select a general type from the values: Dataset, Image, Collection, Service, and Interactive Resource.

<details>
<summary><b>Examples</b></summary>

***Example 1:***</br>
*Title*: Abandoned Mine Land Inventory Points: Pennsylvania, 2018</br>
Description: Abandoned Mine Land Inventory Points: Pennsylvania, 2018. This data set portrays the approximate location of Abandoned Mine Land Problem Areas containing public health, safety, and public welfare problems created by past coal mining. It is a subset of data contained in the Office of Surface Mining (OSM) Abandoned Mine Land Inventory. This layer identifies AML Points representing specific locations within an AML Inventory Site, examples include AML discharge.</br>
*Type:* Dataset</br>

***Example 2:***</br>
*Title:* Metropolitan District: Baltimore County, Maryland, 2017</br>
*Description:* Metropolitan District: Baltimore County, Maryland, 2017. The MetroDistrict feature class contains the current land area included in the metropolitan district in Baltimore County. The metropolitan district is a land planning area determines the status of whether or not a property is eligible for public water and sewer services. The metropolitan district geodatabase includes the MetroDistrict, MetroDistrictLine, MetroDistrictPoly, MetroDistrictMargin feature classes and BCMDLineage table.</br>
*Type:* Dataset</br>

***Example 3:***</br>
*Title:* Ames Library of South Asia Maps</br>
*Description:* A collection of maps covering Asia held by the University of Minnesota's Ames Library of South Asia.</br>
*Type:* Collection</br>

***Example 4:***</br>
*Title:* Baltimore and its neighborhood: an excursion map; compiled for the Johns Hopkins University; based upon the triangulation of the U.S. Coast and Geodetic Survey and published maps of local surveys, 1884</br>
*Description:* Baltimore and its neighborhood: an excursion map; compiled for the Johns Hopkins University; based upon the triangulation of the U.S. Coast and Geodetic Survey and published maps of local surveys, 1884. Depths shown by soundings and isolines. Contours in water one fathom apart. One map: color, mounted on cloth; 60 x 60 cm. This map is in the public domain. Please attribute access and use of this digitized map to the Stephen S. Clark Library, University of Michigan Library.
*Type:* Image
</details>

## Creator

**URI:** dc_creator_sm<br>
**Required:** No<br>
**Repeatable:** Yes<br>
**Type:** Array<br>
**Displayed:** Yes

**Description:** The person(s) or organization(s) that created the resource. The dc_creator_sm field is best reserved for instances in which an individual person has collected, produced, or generated analyses of data (as opposed to an agency releasing a data product or resource).

**Controlled Vocabulary:** [Library of Congress Name Authority File](http://id.loc.gov/authorities/names.html)

**Instructions:** If possible, use authorized heading(s) from the Library of Congress Name Authority File for the creator field.

If there is no established heading created for the publisher:

1. Check the geoportal to see if a local form of the creator’s name has already been established. If so, use that form of the name.

2. Create a local version of the creator’s name according to the Library of Congress Name Authority Guidelines summarized in [Appendix B](#Appendix_B). For clarification, help, or more details on how to construct names, please contact Technical Services.

***Example 1:***<br>
*Title:* A new map of Louisiana with its canals, roads and distances from place to place along the stage and steam boat routes, 1839<br>
*Information on map:* By H.S. Tanner; Published by H.S. Tanner, No.144 Chestnut Street, Philadelphia<br>
*Publisher(s):* Tanner, Henry Schenck, 1786-1858<br>
*Date Issued:* 1839<br>
*Creator(s):* Tanner, Henry Schenck, 1786-1858

***Example 2:***<br>
*Title:* Tree Canopy Change, Derived from Planimetric Land Cover & Elevation Data: Prince George's County, Maryland, 2009-2014<br>
*Description:* Tree Canopy Change, Derived from Planimetric Land Cover & Elevation Data: Prince George's County, Maryland, 2009-2014. This layer is a high-resolution tree canopy change-detection layer for Prince George’s County, Maryland. It contains three tree-canopy classes for the period 2009-2014: (1) No Change; (2) Gain; and (3) Loss. It was created by first mapping tree canopy in 2014 using LiDAR and multispectral data and then comparing the new map directly to an existing tree-canopy map for the year 2009. Tree canopy that existed during both time periods was assigned to the No Change category while trees removed by development, storms, or disease were assigned to the Loss class. Trees planted during the interval were assigned to the Gain category, as were the edges of existing trees that expanded noticeably. Direct comparison was possible because both the 2009 and 2014 maps were created using object-based image analysis (OBIA) and included similar source datasets (LiDAR-derived surface models, multispectral imagery, and thematic GIS inputs). No accuracy assessment was conducted, but the dataset was subjected to comprehensive manual review and correction.<br>
*Publisher(s):* Prince George's County (Md.). Planning Department<br>
*Creator(s):* University of Vermont. Spatial Analysis Laboratory

***Example 3:***<br>
*Title:* Gap Analysis Program, Avian Habitat Models 90 Meter: Pennsylvania, 2000
*Date Issued:* 2000<br>
*Creator(s):* Myers, Wayne L., 1942-|Bishop, Joseph A.<br>
*Publisher(s):* Pennsylvania State University. Institutes of the Environment

## Date Issued

**URI:** dct_issued_s<br/>
**Required:** No<br/>
**Repeatable:** No<br/>
**Type:** String<br/>
**Displayed:** Yes

**Description:** The publication date for the resource.

**Instructions:** If available, add the publication date in form YYYY or YYYY-MM-DD.  Use one dash without any additional spaces between the year, month, and day integers.

***Example 1:***<br/>
*Title on map:* Bird's eye view of the city of Annapolis, Md<br/>
*Date information on map:* Entered according to Act of Congress A.D. 1864 by Chas Magnus to the Clerks Office of S. District of New York<br/>
*Date Issued:* 1864

***Example 2:***<br/>
*Title on map:* The battle of Baltimore by land and sea<br/>
*Date information on map:* N/A<br/>
*No Date Issued element used*

***Example 3:***<br/>
*Title:* Tree Canopy Change, Derived from Planimetric Land Cover & Elevation Data: Prince George's County, Maryland, 2009-2014<br/>
*Temporal coverage:* 2009-2014<br/>
*Date Issued:* 2015-02-10

### Language

**URI:** dc_language_sm<br/>
**Required:** No<br/>
**Repeatable:** Yes<br/>
**Type:** Array<br/>
**Displayed:** Yes

**Description:** Indicates the language(s) of the data or map.

**Instructions:** For resources with textual information, spell out the name of its language(s) in English (e.g., "French"). Capitalize the first letter of the language. Do not include punctuation.

***Example 1:***<br/>
*Title:* Portugallia et Algarbia quae olim Lusitania, 1645<br/>
*Languages of map:* Latin title and text on recto of map; French text on verso of map<br/>
*Language:* Latin|French

***Example 2:***<br/>
*Title:* Yamato zenkoku chizu Henshû nin Nakajima Kahê, 1882<br/>
*Language:* Japanese

***Example 3:***<br/>
*Title:* Address Points: Washington County, Ohio, 2015<br/>
*Language:* English

### Layer ID
**URI:** layer_id_s<br>
**Required:** No<br>
**Repeatable:** No<br>
**Type:** String<br>
**Displayed:** No

**Description:** The Layer ID is used to point to specific layers within a geospatial web service. It indicates the layer id for any WMS or WFS web services listed in the dct_references_s field.

**Instructions:** Only the layer name is added here. The base service endpoint URLs (e.g. "https://maps-public.geo.nyu.edu/geoserver/sdr/wms") are added to the dct_references_s field. Please note: **This value is only used when you have WxS services listed in your external references (dct_references_s)**. This field is not used for ArcGIS Rest Services.

***Example:***

*Title:* Cemeteries: Howard County, Maryland, 2014<br>
*References:* {\"http://www.opengis.net/def/serviceType/ogc/wfs\":\"https://data.howardcountymd.gov/geoserver/wfs\",\"http://www.opengis.net/def/serviceType/ogc/wms\":\"https://data.howardcountymd.gov/geoserver/wms\",\"http://schema.org/url\":\"https://data.howardcountymd.gov/\"}<br>
*Layer ID:* general:Cemeteries

### Modified Date

**URI:** layer_modified_dt<br/>
**Required:** No<br/>
**Repeatable:** No<br/>
**Type:** Date-time<br/>
**Displayed:** No

**Description:** Last modification date for the metadata record.

**Instructions:** This date-time value is generated through a script during the import of the JSON files. Do not manually enter this element.

For the individual creating and/or running the script: Use the XML Schema dateTime format (YYYY-MM-DDThh:mm:ssZ) to indicate when the metadata was last modified. Do not use this field to show when the resource itself was last modified. This field is solely concerned with the modification of the JSON metadata record.

### Publisher

**URI:** dc_publisher_sm <br>
**Required:** No<br>
**Repeatable:** Yes<br>
**Type:** Array<br>
**Displayed:** Yes

***Description:*** The organization that made the original resource available.

***Controlled Vocabulary:*** [Library of Congress Name Authority File](http://id.loc.gov/authorities/names.html)

***Instructions:***  The concept of publisher for digital resources is not well defined. For the Geospatial data in our projects, we indicate the administrative body that made the resource available. Choose from the name of a state, county, city, regional organization, NGO, or University/College. Example: “State of Maryland” or “University of Minnesota.” Often, the Creator element will further refine a more generic Publisher element. For instance: Publisher: State of Iowa; Creator: Iowa Department of Transportation.

If possible, use an authorized heading from the Library of Congress Name Authority File for the publisher field. For print resources digitized by JHU, **do not** include JHU as a publisher. Instead, list the original publisher(s) of print resources in the publisher field. You may, however, list JHU as the publisher for born-digital resources where JHU is the primary organization making the resource available.

If there is no established heading created for the publisher:

1. Check the geoportal to see if a local form of the publisher’s name has already been established. If so, use that form of the name.

2. If no name exists in the geoportal, create a local form of the publisher’s name according to the Library of Congress Name Authority Guidelines summarized in [Appendix B](#Appendix_B). For clarification, help, or more details on how to construct names, please contact Technical Services.

***Example 1:***<br>
*Title:* Geological map of Duluth and vicinity, St. Louis County, Minnesota: Bedrock Geology, 1964<br>
*From map:* Geological map of Duluth and vicinity, St. Louis County, Minnesota: Bedrock Geology by Richard B. Taylor; Geological Survey Paul K. Sims, Director<br>
*Publisher:* Minnesota Geological Survey<br>
*Creator:* Taylor, Richard B.

***Example 2:***<br>
*Title:* Wooded Areas: Allegheny County, Pennsylvania, 2011<br>
*Publisher:* Allegheny County (Pa.)<br>
*Creator:* Allegheny County (Pa.). Division of Computer Services. Geographic Information Systems Group

***Example 3:***<br>
*Title:* Lidar-Derived Shaded Relief, Maryland Imap: Baltimore County, Maryland, 2015<br>
*Publisher:* Maryland<br>
*Creator:* Maryland. Department of Information Technology. Geospatial Services Team

### Solr Year
**URI:** solr_year_i<br/>
**Required:** No<br/>
**Repeatable:** No<br/>
**Type:** Integer<br/>
**Displayed:** No

**Description:** A four digit integer representing a year of temporal coverage or date issued for the resource.

**Instructions:** If possible, use the earliest year from the Temporal Coverage field for this element. If no Temporal Coverage field exists, then use the year from the Date Issued field. If no date is provided for the Solr Year element, it will default to 9999, so an estimate is preferred.

***Example 1:***<br/>
*Temporal Coverage:* 1900-1910<br/>
*Date Issued:* 1911<br/>
*Solr Year:* 1900

***Example 2:***<br/>
*Temporal Coverage:* 1983<br/>
*Date Issued:* 1983<br/>
*Solr Year:* 1983

***Example 3:***<br/>
*Temporal Coverage:* Unknown<br/>
*Date Issued:* 2011<br/>
*Solr Year:* 2011


### Suppressed
**URI:** suppressed_b<br/>
**Required:** No<br/>
**Repeatable:** No<br/>
**Type:** Boolean<br/>
**Displayed:** No

**Description:** This element determines if the resource will appear in search results. If set to True, the record will not appear in search results, however, the record will still be accessible from the Data Relations widget and via direct URL. This field is used primarily for multipart items with identical metadata, such as pages in an atlas or series.


**Controlled Vocabularies:** Boolean values of either True or False.

**Instructions:** Set to either “True" or "False". If set to True, the record will not appear in search results. It is still accessible from the Data Relations widget and via direct URL. If set to False, the record will appear in search results.




### Temporal Coverage
**URI:** dct_temporal_sm<br>
**Required:** No<br>
**Repeatable:** Yes<br>
**Type:** Array<br>
**Displayed:** Yes

**Description:**  This represents the "Ground Condition" of the resource, meaning the time period data was collected or is intended to represent. Displays on the item page in the Year value.    

**Instructions:**  Record temporal coverage using one of the following formats: YYYY or YYYY-YYYY. If possible, provide a precise year or year range like “1901”, “1970-1973”. If unsure of exact dates, provide a close estimate using a year range rounded up or down to the nearest decade. Examples include:  “1900-1910”, “1750-1800”, “1800-1900”, “1930-1960”, “1880-1900”. Use one dash without any additional spaces to separate date ranges. Always use four integers (YYYY) for years. If completely unknown, do not include this element.

***Example 1:***<br>
*Temporal Coverage:* 1900-1910<br>
*Date Issued:* 1911<br>
*Solr Year:* 1900<br>

***Example 2:***<br>
*Temporal Coverage:* 1983<br>
*Date Issued:* 1983<br>
*Solr Year:* 1983<br>

***Example 3:***<br>
*Temporal Coverage:* Unknown<br>
*Date Issued:* 2011<br>
*Solr Year:* 2011<br>



### Appendix A: Mappings

### Appendix B: Constructing names                    
#### Instructions for personal names

1. When choosing a name for a person, generally choose the most updated name by which the person is commonly known.  If no information on the person is publically available, use the name as it appears on the source.

2. Record the surname as the first element, followed by a comma and other parts of the name.
  - Bernhardt, Sarah
  - Ching, Francis K. W.
<br><br>
3. Record similar terms (e.g., *Jr.*, *Sr.*, *fils*, *père*) and numbers (e.g., *III*) following the person’s given name or names, preceded by a comma.
  - Saur, Karl-Otto, Jr.
  - Hatfield, Frederick C., II
<br><br>
4. Record accents and other diacritical marks if they appear in the source of information or if they are integral to a name.
  - Lévi, Éliphas, 1810-1875
<br><br>
5. Record birth and death dates of a person if known.
  - Wiśniewski, Cleo, -1990
  - Nakayama, Elle, 1923-2005
  - Mwangi, Sarah, 1975-
  - Smith, Agnes, 1900?-1998
<br><br>
6. Leave a space between each initial and another part of the name. Don’t leave a space between an initial and a comma.
  - Du Bois, W. E. B. (William Edward Burghardt), 1868-1963
  - Franco G. S., José Fernándo
<br><br>
7. In general, omit terms of honour and terms of address (Dr., Mr., Mrs., Frau, Madame) from the name.

8. Check to make sure your constructed name is not already taken by another person in LCNAF. If so, try adding more detail to your name by expanding initials or adding dates.


#### Instructions for corporate bodies

1. Choose the name by which the corporate body is commonly identified.
  - Topographical Survey Commission of Baltimore City
  - North Carolina Floodplain Mapping Program
<br><br>
2. Capitalize each separate word or initial in the name except articles, prepositions, and conjunctions. Do not capitalize an article preceding the name, even when it is part of the official name.

3. If a corporate body is a subdivision, department, or component of a parent body, record the name as a subdivision of the higher or related body.
  - Canada. Topographical Survey Directorate
  - Baltimore (Md.). Bureau of Plans & Surveys
  - Johns Hopkins University. School of Advanced International Studies
<br><br>
4. Record accents and any other diacritical marks appearing in a name.
  - Latitude 40° Inc.
  - Dépot des cartes de la ville de Genève
<br><br>
5. Record initials without spaces.
  - J.P.K. Maps (Firm)
  - U.S. Lake Survey
<br><br>
6. If needed to distinguish one authorized access point from another (i.e., when two or more bodies have the same name or names so similar that they may be confused), include the name of the place associated with the corporate body.
  - Sociedad Nacional de Agricultura (Chile)
  - Governor’s Conference on Aging (Ariz.)
  - Newport High School (Newport, Ky.)
  - Newport High School (Newport, Wash.)
<br><br>
7. If there is no associated place, include a date associated with a corporate body if needed to distinguish one authorized access point from another.
  - South Dakota. Department of Public Safety (1973–1984)
  - South Dakota. Department of Public Safety (2003– )
<br><br>
8. If there is no associated place or date, include a designation if it assists in the understanding of the nature or purpose of the body.
  - Maps & Atlases (Musical group)
  - Maps and Agencies (Firm)
<br><br>
9. Check to make sure your constructed name is not already taken by another corporate body in LCNAF. If so, try adding more detail to your name by adding dates, a location, or a designation.

### Appendix C
#### Deriving descriptions from MARC records:

If deriving description from a MARC record, concatenate the following components together if available to create the description. Expand abbreviations if possible. This may be done with a script.

 Description = *title component* + *summary component* + *scale component*.
 See below on how to formulate each component.

*Title component:*
- Concatenate fields below in the following order:
  - 245 field, subfields $a +$b: Title
  - 260/264, subfield $c: Date of Publication
    - Do not add questionable publication dates. Remove all copyright symbols.
  - 246 field(s), subfield $a: Varying Form of Title
  - 250 field(s): Edition Statement

*Summary component:*
- Concatenate fields together in the following order. Skip any field(s) that will not be relevant or coherent in a digital context.
  - 520 field(s): Summary note
  - 500 field(s): General note
  - 505 field(s): Formatted contents note
  - 515 field(s): Numbering peculiarities note
  - 525 field(s): Supplement note
  - 59x field(s): Local JHU notes

*Scale component:*
- Select one of the following to comprise the scale component:
  - 255 field, subfield $a: Cartographic Mathematical Data
  - 507 field:  Scale Note for Graphic Material

***Example 1:***

*MARC fields:*

245 $a Plan of Baltimore & vicinity : b| showing the proposed routes for bringing water from the Jones and Gwynns Falls & the Patapsco River /
246 $a Plan of Baltimore and vicinity
255 $a Scales of feet and miles [ca. 1:24,000].
500 $a Relief shown by hachures and soundings.
500 $a "Lith. by A. Hoen & Co. ... Baltimore."
500 $a Identifies actual and proposed water resources, storage facilities, pipes and conduits, as well as roads and buildings in Baltimore and north and west of the city.

*JHU-GMAP description:*

Plan of Baltimore & vicinity: showing the proposed routes for bringing water from the Jones and Gwynns Falls & the Patapsco River, 1853. Relief shown by hachures and soundings. Identifies actual and proposed water resources, storage facilities, pipes and conduits, as well as roads and buildings in Baltimore and north and west of the city. Scales of feet and miles, approximately 1:24,000.

*Explanation:*

Did not include 246, as it does not differ greatly from main title. Did not include second 500 field as it would not make sense out of context. Removed brackets from field 255, and expanded abbreviation “ca.” to approximately.

***Example 2:***

*MARC fields:*

245 $a Virginia, Marylandia et Carolina in America septentrionali Britannorum industria excultae /
255  $a Scale of German miles (Milliaria Germanica) [ca. 1:2,000,000].
260 $c [1759?]
500 $a Hand colored.
500 $a Relief shown pictorially and by hachures. Depths shown by soundings.
500 $a From Homann Erben's Atlas geographicvs maior exhibens tellurem. 1753-1759 [i.e. 1781; v. 1, 1759].
500 $a Described in William P. Cumming's The southeast in early maps as first appearing in J.B. Homann's Atlas novus, 1714.
500 $a Also covers New Jersey, Southern Pennsylvania, and New York City region.
500 $a Includes engraved ill. around title.

*JHU-GMAP description:*

Virginia, Marylandia et Carolina in America septentrionali Britannorum industria excultae. Hand colored. Relief shown pictorially and by hachures. Depths shown by soundings. Described in William P. Cumming's The southeast in early maps as first appearing in J.B. Homann's Atlas novus, 1714. Also covers New Jersey, Southern Pennsylvania, and New York City region. Includes engraved illustrations around title. Scale of German miles (Milliaria Germanica), approximately 1:2,000,000.

*Explanation:*

Did not include third 500 field due to lack of clarity. Expanded abbreviation “ill.” to "illustration" in last 500 field. Removed brackets from field 255, and expanded abbreviation “ca.” to approximately.
