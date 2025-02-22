# Installation

### Versioning

1. adamapi==2.2.2.3, This pachage works only with ADAMCORE 2.

## Requirements

This package requires python>=3.6.

```bash
python3 -m pip install --upgrade pip
pip install adamapi
```

# API DEFINITIONS
This document briefly describes the ADMAPI functionalities.<br>
The ADAMAPI library is divided in 4 modules:
1.  Auth --> the authorization module
2.  Datasets --> to get the list of datasets
3.  Search --> to get the lists of products, including associated metadata (e.g. geometry, cloud cover, orbit, tile, ...)
4.  GetData --> to retrieve the product(s). It includes options for subsetting products in space and time, for downloading at native data granularity and with reduced processing capacity

## 1 - Auth
This module takes care of user authentication and authorization.<br>
Without instancing an object of this module other components don't work.<br>
Auth module is based on the ADAMAPI_KEY, a key that uniquelly identifies the user.

**Class contructor and parameters**
```python
from adamapi import Auth
a = Auth()
```
Parameters:<br>

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| | | | | |

**Public methods and parameters**

*  **.setKey()** --> To setup the ADAMAPI_KEY<br>
Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | str | | The ADAMAPI_KEY |

*  **.setAdamCore()** --> To setup the url of the ADAM-CORE endpoint<br>
Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | str | | The url like https://test.adamplatform.eu |

*  **.authorize()** --> to instanciate an auth object<br>
Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| | | | | |


*  **.getAuthToken()** --> to get the authorization token<br>
Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| | | | | |


### 1.1 - ADAMAPI_KEY retrieval
To get the ADAMAPI_KEY, you need to access your ADAM portal and:<br>
1.  Select the "user icon" on the top right
2.  Expand / click the "USERNAME"
3.  Click on the "Api Key" to display your key
<br>
*Command-line ADAMAPI_KEY retrieval TBP*

### 1.2 - ADAMAPI_KEY setup
There are three methods to setup the ADAMAPI_KEY and the ADAM-CORE instance:
1. use the method setKey() and setAdamCore()
```python
from adamapi import Auth
a = Auth()
a.setKey('<ADAMAPI_KEY>')
a.setAdamCore('https://test.adamplatform.eu')
```
2. Export two envars like
```bash
#open a Terminal and type:
export ADAMAPI_KEY='<ADAMAPI_KEY>'
export ADAMAPI_URL='https://test.adamplatform.eu'
```
3. create a file called **.adamapirc** in the user home directory with the following content
```text
key=<ADAMAPI_KEY>
url=https://test.adamplatform.eu
```
### 1.3 - Examples
After ADAMAPI_KEY has been set up, an auth instance can be created with:
```python
from adamapi import Auth
a = Auth()
a.authorize()
```
After authorize method you can retrive your autho token:
```python

from adamapi import Auth
a = Auth()
a.authorize()
a.getAuthToken()
```

## 2 - Datasets
This module provides datasets discovery functionality.

**Class contructor and parameters**
```python
from adamapi import Datasets
datasets = Datasets( a )
```
Parameters:<br>

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True |  Auth instance | | The ADAMAPI authorized instance obtained in the previous section |

**Public methods and parameters**

*  **.getDatasets()** --> To retrieve datasets list <br>
Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | False | str | | The datasetId. |
| page | False | numeric | 0 | Indicats a specific page  |
| maxRecords | False | numeric | 10 | Max number of results in output.  |

This .getDatasets() function can be used to retrive additional filters which are described in the key **filtersEnabled** (if exists).

### 2.1 Examples
This module can be used in 2 different ways.

1.  To list all available datasets:
```python
datasets = Datasets(a)
print(datasets.getDatasets())
```
2.  To get detailed metadata about a specific dataset
```python
datasets = Datasets(a)
print( datasets.getDatasets( '{{ID:DATASET}}' , page=0 , maxRecords=10 ) )
```
3. To get filtersEnabled. To use this additional filters see first example in Search section.
```python
datasets = Datasets(a)
out=datasets.getDatasets("{{ID:DATASET}}")
print(out["filtersEnabled"])
```

## 3 - Search
This module provides discovery functionality through the products available on the ADAM instance.

**Class contructor and parameters**
```python
from adamapi import Search
search = Search( a )
```
Parameters:<br>

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | Auth instance | | The ADAMAPI authorized instance obtained in section 1-Auth |

**Public methods and parameters**

*  **.getProducts()** --> To retrieve datasets list and metadata<br>

Parameters:

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | str | | The datasetId. |
| maxRecords | False | int | 10 | number of records |
| startIndex | False | int | 0 | starting record index |
| startDate | False | str or [datetime](https://docs.python.org/3/library/datetime.html) | | the start date |
| endDate | False | str or [datetime](https://docs.python.org/3/library/datetime.html) | | the end date |
| geometry | False | str or geojson |  | GeoJson geometry,[geojson format](https://tools.ietf.org/html/rfc7946) [appendix](#geometry)|


### 3.1 Examples

1. Example1:
```python
search=Search(a)
mongo_search=search.getProducts('{{ID:DATASET}}',maxRecords=1,startIndex=0,platform="{{VALUE}}")
```
2. Example2:
```python
search=Search(a)
mongo_search=search.getProducts('{{ID:DATASET}}',maxRecords=1,startIndex=0)
```

## 4 - GetData
This module provides data access of raster, spatial subset, timeseries in the native data granularity and reduced processing capacity.

**Class contructor and parameters**
```python
from adamapi import GetData
data=GetData(a)
```
Parameters:<br>

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | Auth Instance |  | The ADAMAPI authorized instance obtained in the section 1-Auth |

**Public methods and parameters**

*  **.getData()** --> To retrieve a specific product or a dataset in its native granularity, to get a subset of it, to perform a timeseries or to exec simple processing <br>

| position/keyword | mandatory | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| 0 | True | str |  | The datasetId |
| 1 | True | str | GetFile | request type. available values: GetFile,GetSubset, GetTimeseries and GetProcessing |
| asynchronous | False | boolean | False | rappesents how the request will be performed |
| compress | False | boolean | False | return a zip file |
| rest | False | boolean | True | perform RESTful order ignoring explorer state on the server and equalization configured using the explorer gui |
| filters | True | json | {} | json object with filters parameter. startDate and endDate are required inside it. Geometry is not required for GetFile operation, it is otherwise|
| options | False | json | {} | request option |
| outputDir | False | str | `adamapiresults/` | set a different download directory inside `adamapiresult/` main directory |

### 4.1 Examples

```python
data=GetData(a)
#to retrive a specific product
image = data.getData('{{ID:DATASET}}',"GetFile",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"productId":'{{PRODUCTID}}'},outputDir='{{OUTPUT_DIR}}')


#to retrieve a dataset in its native granularity
data=GetData(self.a)
image = data.getData('{{ID:DATASET}}',"GetFile",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"geometry":'{{GEOMETRY}}'},outputDir='{{OUTPUT_DIR}}')
```

For the GetSubset,GetTimeseries and GetProcessing requests you need to add the `options` parameter with these constraints : [output formats](#output-formats) and [functions](#processing-function)(only for processing request)
```python
#subset example
image = data.getData('{{ID:DATASET}}',"GetSubset",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"geometry":'{{GEOMETRY}}'},options={"format":'{{FORMATS}}'},outputDir='{{OUTPUT_DIR}}')

#timeseries example
image = data.getData('{{ID:DATASET}}',"GetTimeseries",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"geometry":'{{GEOMETRY}}'},options={"format":'{{FORMATS}}'},outputDir='{{OUTPUT_DIR}}')

#processing example
image = data.getData('{{ID:DATASET}}',"GetProcessing",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"geometry":'{{GEOMETRY}}'},options={"format":'{{FORMAT}}',"function":'{{FUNCTION}}'},outputDir='{{OUTPUT_DIR}}')

```

### 4.3 Asyncronous Example
```python
#1. execute the request
image = data.getData('{{ID:DATASET}}',"GetSubset",asynchronous=False,compress=False,rest=False,filters={"startDate":'{{STARTDATE}}',"endDate":'{{ENDDATE}}',"geometry":'{{GEOMETRY}}'},options={"format":'{{FORMATS}}'},outputDir='{{OUTPUT_DIR}}')


#2. check the status

stat=data.getData(datasetId,"GetSubset",asynchronous=True,id=str(image.pk))
while stat.status != "completed":
    time.sleep(1)
    stat=data.getData(datasetId,"GetSubset",asynchronous=True,id=str(image.pk))

#3. download the zip,unzip it and remove the zip (optional)
for res in stat.list:
    if res["status"] == "failed":
        print(res["exit_code"])
    else:
        r=self.a.client(res["download"]["url"],{},"GET")
        with open(str(res["download"]["url"].split("/")[4])+"_"+str(res["download"]["url"].split("/")[5]), 'wb' ) as f:
            f.write( r.content )

```



# Appendix 1 - Data format
## date and date+time
Supported string date/date+time format are:
*  '%Y-%m-%dT%H:%M:%S',
*  '%Y-%m-%dT%H:%M:%SZ',
*  '%Y-%m-%d'

### GeoJson
Geometry have to follow the latest geojson standard [rfc7946](https://tools.ietf.org/html/rfc7946)<br>
In particular Polygons and MultiPolygons should follow the right-hand rule<br>

### Geometry
```python
#This geometry will return all the results it has intersected within it
geometry = { "type": "Polygon", "coordinates": [ [ [ 43.916666667, 15.716666667 ], [ 43.916666667, 15.416666667 ]    , [ 44.216666667, 15.416666667 ], [ 44.216666667, 15.716666667 ], [ 43.916666667, 15.716666667 ] ] ] }
```

```python
#This geometry will return all the results it has intersected on its outside
geometry = { "type": "Polygon", "coordinates": [ [ [ 43.84986877441406,15.925676536359038 ], [ 44.6539306640625,15.950766025306109 ],[ 44.681396484375,15.194084972583916 ], [ 43.8189697265625,15.20998780073036 ], [ 43.84986877441406,15.925676536359038 ] ] ] }
```

### Output Formats

| request | output format |
| ---- | ---- |
| GetFile | - |
| GetSubset | tiff,png |
| GetTimeseries | json,csv |
| GetProcessing **experimental** | tiff,png |

### Processing Function

| type | description |
| ---- | ---- |
| average | When the GetProcessing retrieves a multi-band product or a set of products it executes the average of their values |
| overlap | When the GetProcessing retrieves a set of products, it executes their overlap without any specific strategy |
| mosterecent | When the GetProcessing retrieves a set of products, it puts on the top the most recent one |
| leastrecent | When the GetProcessing retrieves a set of products, it puts on top the least recent one |
| minvalue | When the GetProcessing retrieves a multi-band product or a set of products for each pixel it puts on top the minimum value of the pixel |
| maxvalue | When the GetProcessing retrieves a multi-band product or a set of products for each pixel it for each pixel, puts on top the maximum value of the pixel |
