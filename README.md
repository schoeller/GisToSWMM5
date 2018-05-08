# GisToSWMM5

GisToSWMM5 is a tool for automatically constructing SWMM5 model descriptions. The input files for the tool can be prepared using GIS software and the resulting SWMM model can be studied in GIS. The tool takes the digital elevation model, land-use, and flow direction information from the user-prepared input files, creates subcatchments for the studied area, and routes the water between subcatchments and into the stormwater network.

The tool was originally developed by Lassi Warsta (lassi@warsta.net) at Aalto University School of Engineering and is distributed under MIT-license.

When using the GisToSWMM5 tool, please refer to the following publication:  
*Warsta, L., Niemi, T. J., Taka, M., Krebs, G., Haahti, K., Koivusalo, H., & Kokkonen, T. 2017. Development and application of an automated subcatchment generator for SWMM using open data. Urban Water Journal.14 (9): 954-963. DOI:[10.1080/1573062X.2017.1325496](https://dx.doi.org/10.1080/1573062X.2017.1325496).*

### Using GisToSWMM5

GisToSWMM5 is easiest to use with a script where the input files are described. An example script is given in [run_scripts](run_scripts) folder. The script file should consist of the following lines, which are the inputs to the GisToSWMM5:

[PATH TO GisToSWMM5 EXECUTABLE] \  
[PATH TO DEM RASTER IN .asc FORMAT] \  
[PATH TO FLOW DIRECTION RASTER IN .asc FORMAT] \  
[PATH TO LANDUSE RASTER IN .asc FORMAT] \  
[PATH TO CATCHMENT PROPERTIES TABLE IN .csv FORMAT] \  
[PATH TO JUNCTIONS TABLE IN .csv FORMAT] \  
[PATH TO CONDUITS TABLE IN .csv FORMAT] \  
[PATH TO OUTFALLS TABLE IN .csv FORMAT] \  
[PATH TO HEADER TABLE IN .csv FORMAT] \  
[PATH TO EVAPORATION TABLE IN .csv FORMAT] \  
[PATH TO TEMPERATURE TABLE IN .csv FORMAT] \  
[PATH TO SNOWPACK TABLE IN .csv FORMAT] \  
[PATH TO RAINGAGE TABLE IN .csv FORMAT] \  
[PATH TO INFLOW TABLE IN .csv FORMAT] \  
[PATH TO TIMESERIES TABLE IN .csv FORMAT] \  
[PATH TO PUMP TABLE IN .csv FORMAT] \  
[PATH TO PUMP CURVE TABLE IN .csv FORMAT] \  
[PATH TO STORAGE TABLE IN .csv FORMAT] \  
[PATH TO DWF TABLE IN .csv FORMAT] \  
[PATH TO PATTERNS TABLE IN .csv FORMAT] \  
[PATH TO LOSSES TABLE IN .csv FORMAT] \  
[PATH TO CROSSECTIONS TABLE IN .csv FORMAT] \  
[PATH TO REPORT TABLE IN .csv FORMAT] \  
[PATH TO SYMBOLS TABLE IN .csv FORMAT] \  
[PATH TO OUTPUT BASE FILENAME]  

GisToSWMM5 can be used to build a SWMM5 description in three modes:
1. each raster cell is an individual sub-catchment
2. cells with the same land-use and same eventual outlet are merged into one sub-catchment
3. (*legacy mode:* neighboring *2<sup>N</sup>* x *2<sup>N</sup>* cells, where *N* = 1, 2, 3, ..., are merged together into one sub-catchment and the most abundant landuse among cells is set as the subcatchment landuse)

In case option 1 used, the run script is as above.  
In case  option 2 is used, an additional line with any integer number is required as the last line of the script:  
[*OPTIONAL* INTEGER VALUE]   
In case option 3 is used, the maximum grid cell size and the allowed number of subdivisions (*N*) are required as the two last lines of the script:  
[*OPTIONAL* MAXIMUM GRID CELL SIDE LENGTH] \  
[*OPTIONAL* NUMBER OF SUBDIVISIONS FOR GRID CELLS]  

DEM, land-use, and flow direction rasters have to be defined with the same grid. The cell size of these grids defines the minimum size of the final sub-catchment grid size. Flow direction raster uses notation where flow directions are marked with numbers from 1-8 so that 1 corresponds to flow to Northeast and numbers increase in counterclockwise direction. More information regarding the required input files is given in [input_files.md](input_files.md) and some (partly outdated) details are also available from:  
Rautiainen, M. 2016. Hulevesimallinnus ja tulvariskin arviointi Turun sataman valuma-alueella. Master’s Thesis. Aalto University, Espoo, Finland. (In Finnish). <http://urn.fi/URN:NBN:fi:aalto-201611025308>

### Output files

GisToSWMM5 produces the following output files:
- a SWMM input file that can be used to run  SWMM simulations  
([PATH TO OUTPUT BASE FILENAME].inp)
- a vector file defining the sub-catchments according to mode 1  
([PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m.wkt, where *L* is the side length of the cells in DEM, land-use, and flow direction rasters)
- a vector file defining the routing between sub-catchments according to mode 1  
([PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m_routing.wkt)
- a vector file defining the sub-catchments (according to mode 1) that are actually routed into the catchment outfall  
([PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m_routed.wkt)
- a vector file defining the routing of the storm-water network  
([PATH TO OUTPUT BASE FILENAME]\_network_routing.wkt)

When GisToSWMM5 is used to create adaptive sub-catchments in mode 2, two additional files are created:
- a raster file defining the adaptive subcatchments  
([PATH TO OUTPUT BASE FILENAME]\_subcatchments.asc)
- a vector file defining the attributes of the adaptive subcatchments  
([PATH TO OUTPUT BASE FILENAME]\_subcatchments_attr.wkt)

The vector files are created using the Well Known Text (wkt) vector format. E.g. QGIS can natively understand .wkt-files. For conversion to shapefiles using e.g. ogr2ogr command line operation, a .csvt file defining the field types of .wkt file is created. The produced vector files allow for easily studying the SWMM model using a GIS software removing the need of using SWMM GUI.

The "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m.wkt" file has the
following attributes:  
"id;";  
"wkt;";&emsp;&emsp;&emsp;&ensp;// Polygon defining the shape of the  sub-catchment  
"name;";&emsp;&emsp;&emsp;&ensp;// Name of the  sub-catchment  
"outlet;";&emsp;&emsp;&emsp;&ensp;// Name of node or another sub-catchment that receives runoff  
"area_m2;";&emsp;&emsp;&emsp;&ensp;// Area of sub-catchment (m2)  
"slope_pct;";&emsp;&emsp;&emsp;&ensp;// Average surface slope (%)  
"elevation;";&emsp;&emsp;&emsp;&ensp;// Elevation of the sub-catchment  
"landuse;";&emsp;&emsp;&emsp;&ensp;// Code for land-use type  
"imp_pct;";&emsp;&emsp;&emsp;&ensp;// Percent of impervious area (%)  
"n_imp;";&emsp;&emsp;&emsp;&ensp;// Manning's N for impervious area (-)  
"n_per;";&emsp;&emsp;&emsp;&ensp;// Manning's N for pervious area (-)  
"S_imp_mm;";&emsp;&emsp;&emsp;&ensp;// Depth of depression storage on impervious area (mm)  
"S_per_mm;";&emsp;&emsp;&emsp;&ensp;// Depth of depression storage on pervious area (mm)  
"suct_mm;";&emsp;&emsp;&emsp;&ensp;// Soil capillary suction head (mm)  
"Ksat_mmhr;";&emsp;&emsp;&emsp;&ensp;// Soil saturated hydraulic conductivity (mm/hr)  
"IMDmax;";&emsp;&emsp;&emsp;&ensp;// Difference between soil porosity and initial moisture content (a fraction)  
"isSink";&emsp;&emsp;&emsp;&ensp;// An indicator showing if the cell is a local sink/pit in the catchment  
"Tag";&emsp;&emsp;&emsp;&ensp;// An optional string description for the landuse class 

The "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m_routing.wkt" and the "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m_routed.wkt" files have the following attributes:  
"id;";  
"wkt;";&emsp;&emsp;&emsp;&ensp;// Line object defining the route between "from" and "to" sub-catchments  
"from;";&emsp;&emsp;&emsp;&ensp;// Name of the origin sub-catchment  
"to";&emsp;&emsp;&emsp;&ensp;// Name of the target sub-catchment  

The "[PATH TO OUTPUT BASE FILENAME]\_network_routing.wkt" file has the following attributes:  
"id;";  
"wkt;";&emsp;&emsp;&emsp;&ensp;// Line object defining the route between "from" and "to" sub-catchments  
"name";&emsp;&emsp;&emsp;&ensp;// Name of the conduit  
"from;";&emsp;&emsp;&emsp;&ensp;// Name of the origin junction  
"to";&emsp;&emsp;&emsp;&ensp;// Name of the target junction  

The "[PATH TO OUTPUT BASE FILENAME]\_subcatchments.asc" file has an integer in each raster cell indicating the corresponding catchment for the cell. If the cell does not belong to catchment -1 is used.   

The format of "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_attr.wkt" file is equal to "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_*L*x*L*m.wkt". The only difference is that "wkt" represents only the cell at the sub-catchment center-of-mass-point, and not the entire sub-catchment. The attribute "id" corresponds to the integer value of sub-catchment in "[PATH TO OUTPUT BASE FILENAME]\_subcatchments.asc".

### Utility tools
Folder 'utils' contains utility programs for various tasks:
- *inp2gis.py* extracts sub-catchment polygons and routing between sub-catchments from a SWMM input file (\*.inp). The tool saves the sub-catchments as a polygon shapefile and the routing between the sub-catchment polygon centroids as a line shapefile.
 - This utility tool is suitable for any SWMM5 input file, i.e., it is not restricted to GisToSWMM5 generated files.
- *rpt2gis.py* extracts sub-catchment polygons from a SWMM5 input file (\*.inp) and sub-catchment simulation results from the corresponding SWMM5 report file (by default \*.rpt). The tool merges the information and saves the sub-catchment results as a polygon shapefile.
 - This utility tool is suitable for any SWMM5 input/report file, i.e., it is not restricted to GisToSWMM5 generated files.
- *ExtractLinkData.py* collates time series data from a given link (in several) SWMM5 report file(s) into a single .csv file.
 - This utility tool is suitable for any SWMM5 report file, i.e., it is not restricted to GisToSWMM5 generated files.
- *adap2shp.py* creates a shapefile of adaptive sub-catchments with given sub-catchment attributes using the information from "[PATH TO OUTPUT BASE FILENAME]\_subcatchments.asc" and "[PATH TO OUTPUT BASE FILENAME]\_subcatchments_attr.wkt" files.
- *ExtractSubcatchmentResults.py* extracts sub-catchment summary results from the SWMM5 report file (by default \*.rpt) and presents them as sub-catchment attributes in a Well-Known-Text file. Particularly useful for creating sub-catchment runoff results shpafile from adaptive model results, as this shapefile cannot be directly produced using the \*.inp and \*.rpt files. To produce the shapefile, first create a \*.wkt file of subcatchment runoff results using *ExtractSubcatchmentResults.py* and then use *adap2shp.py* with the "[PATH TO OUTPUT BASE FILENAME]\_subcatchments.asc" and the newly created \*.wkt file as inputs to produce the final shapefile.
- *wkt2shp.sh* converts all .wkt files in a given folder into shapefiles (e.g. for ArcMap).


The utility tools are written in python 3.\* and bash. They have been tested on Ubuntu Linux 16.04 LTS. The python scripts use the following non-standard packages:
- pandas (ExtractDataSets.py, ExtractSubcatchmentResults.py, inp2gis.py, rpt2gis.py, adap2shp.py)
- geopandas (inp2gis.py, rpt2gis.py, adap2shp.py)
- shapely (inp2gis.py, rpt2gis.py)
- rasterio (adap2shp.py)

Using [Miniconda(/Anaconda)](https://www.anaconda.com/download/) Python distribution is highly recommended. It is an open source distribution of Python, intended especially for data science and machine learning. In practice, it makes life a lot easier when installing Python packages even when dealing with simple scripts such as here.

Example run scripts for GisToSWMM5 and for *ExtractDataSets.py* and *ExtractSubcatchmentResults.py* are given in [run_scripts](run_scripts) folder.  

While using inp2gis.py and rpt2gis.py is relatively simple in Linux environment, in Windows environment it is easiest to use them with batch scripts. Batch scripts for running these two utility programs are therefore given in  [run_scripts](run_scripts) folder.  

In case 'activate' command for Miniconda(/Anaconda) does not work from command line on Windows, or the batch scripts complain that 'activate' is not recognized as a valid command, the quick fix is to add 'C:\Users\\[USER]\Miniconda3\Scripts' or 'C:\Users\\[USER]\Anaconda3\Scripts' to the end of your PATH environmental variable.  
(On Windows 10: Go to Control Panel -> search for 'path' -> Edit environment variables for your account -> Select 'Path' -> Edit -> New -> Add the path to the Scripts folder -> OK -> OK)

### Demo case
Folder [demo_catchment](demo_catchment) contains an example dataset and run scripts for GisToSWMM5 using a small imaginary catchment. Following subfolders are included:
- [data](demo_catchment/data) contains the necessary files required to run GisToSWMM5.
- [GIS](demo_catchment/GIS) contains files corresponding to [data](demo_catchment/data) files in a (more) GIS-friendly format alongside a QGIS-project file with the files already imported.
- [run_scripts](demo_catchment/run_scripts) contains example scripts to run GisToSWMM5 using the given data, and to collect the sub-catchment runoff results and the result timeseries from the SWMM output files.
- [out](demo_catchment/out) contains the output files of GisToSWMM5 in [SWMM_in](demo_catchment/out/SWMM_in), the output files of SWMM in [SWMM_out](demo_catchment/out/SWMM_out), and finally the SWMM result time series in [SWMM_results](demo_catchment/out/SWMM_results).
- [QGIS_styles](demo_catchment/QGIS_styles) contains QGIS style definition files that may be helpful in visualizing the GisToSWMM5 input and output files in QGIS.
