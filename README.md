# Azure Data Factory Tutorial

- Ready Data Factory Pipelines
   - https://github.com/atingupta2005/adf-apr-21.git

- Create a new Azure Data Factory resource in Azure and specify details of Github URL specified above while creating your Data Factory

## 1-azure-data-factory-mapping-data-flows-basics (basics branch)
1. Create blank SQL Database - (dbatinapr21)
```
Server Name: sqlsvrapr21.database.windows.net
```

- Note: make sure to enable firewall settings in Networking section to Public Endpoint and Allow Client IP and Azure Services
	
1. Create cars table in the database
```
CREATE TABLE Cars (
	Make nvarchar(100),
	Model nvarchar(200),
	Type nvarchar(100),
	Origin nvarchar(100),
	DriveTrain nvarchar(100),
	Length decimal(18,0)
)
```

1. Create a new Data Factory (adfatinapr21)
1. Create storage account (sadfapr21), create container (input) and upload cars.csv to it
1. Create linked service to connect to storage account - (lsStorage)
1. Create linked service to connect to sql server - (lsSQL)
1. Add dataset to connect to sql table - (outTable)
1. Create dblob dataset to connect to cars.csv	- (inputCSV)
1. Create pipeline
1. Add copy data activity to the pipeline
1. Configure copy data activity
1. Run pipeline
1. Verify data is there in the cars table
```
select * from [dbo].[Cars]
```

## 2-azure-data-factory-parametrization (parametrization branch)
1. Create planes tables in SQL database
```
CREATE TABLE Planes (
	ICAO nvarchar(200),
	IATA nvarchar(200),
	MAKER nvarchar(200),
	DESCRIPTION nvarchar(200)
)
```

1. Add parameter to dataset - inputCSV
```
fileName
```
1. Use parameter in the connection tab
1. Add parameter to dataset - outputTable
```
tableName
```
1. Upload planes.csv to storage account\input container
1. Use parameter in the connection tab
1. Now add parameters to the pipeline itself
1. Use those parameters in copy data activity
1. Now while running pipeline, it will ask the inputs
1. Add trigger to pipeline
1. Publish pipeline
1. Run pipeline using trigger
1. Monitor pipeline execution in Monitor tab


## 3-azure-data-factory-mapping-data-flows (data-flows branch)
1. Create another container in storage account - output
1. Upload movies.csv to input
1. Create/update linked services
1. Create dataset to input\movies.csv (dsInMovies)
1. Create dataset to output\ (dsOutMovies)
	- Select first row as header
	- Disable import schema
1. Create a Data Flow resource
1. Add Source -> dsInMovies dataset
1. Enable Data flow debug
1. Wait for 5-10 min
1. Select step and open tab Data Preview
1. Add new step - Derived Column. Name it - YearTitleExtraction
1. Enter column Name (Year) and Add expression to it
```
toInteger(trim(right(title, 6), '()'))
```
1. Preview data
1. Add new column - Title and add expression
```
toString(left(title, length(title)-6))
```
1. Preview the data on each step to verify changes
1. Add new Step - Sink
1. Specify dataset - dsOutMovies
1. Add a new Pipeline (pipeline2) and add Dataflow to it
1. Run Pipeline and Refer to output container and open file - part*
1. To create a single file open dataflow
1. Open sink step and specify "Output to Single File" in Settings tab


## 4-azure-data-factory-self-hosted-integration-runtime - (ir branch)
1. Create a data factory
1. Open Manage Tab\connections\intergration runtimes and create new
1. Create Self Hosted Integration runtime (irSelfHosted)
1. Download and install on VM
```
https://www.microsoft.com/en-us/download/details.aspx?id=39717
Version: 5.4
```
1. Register by specifying the key
1. Verify the connection status in Portal
1. Create a folder on the VM:
```
C:\ir
```
1. Create a linked service to read file (lsirFile)
```
Integration Runtime: Self Hosted
Host: C:\ir
Username: dsvmApr21\atingupta2005
Password: <Password of VM>
```
1. Put a cars.csv in C:\ir
1. Create a dataset of type Fileservice and connect to that file (dsirInput)
1. Create a dataset of type Fileservice and connect to the folder and specify a new file name - carscopy.csv (dsirOut)
1. Create a pipeline (pipeline2) and Copy Data activity and configure it
1. Notice that we can copy content on the VM


## 5-Get Metadata Activity (branch getMetaData)
1. Create/Update the Linked Service
1. Create a new Pipeline (pipeline3)
1. Add activity - Get Metadata
1. Connect to the File Linked Service created using Self Hosted Runtime
1. Create a dataset to represent files in folder (dsFolderIn)
1. This dataset should refer to the folder - c:\ir
1. Connect activity with dataset and add new Field in the activity - Child items
1. Publish and Run pipeline
1.  Inspect the output JSON
1. Add a foreach activity
1. In Setting\Items Add dynamic content and put below expression:
```
@activity('Get Metadata1').output.childitems
```
1. Create a variable in pipeline level - (fileName). It will be used in ForEach
1. Open For each activity
1. Add Set Variable activity
1. Use @item().name to refer to the current item in For each loop
1. Debug pipeline and verify the output

## 6-Storage Events Trigger (6-storage-events-trigger branch)
- Pending instructions

## 7-Filter Activity (Filter_if_append branch):
1. Create/Update Linked Service
1. Create a new dataset to refer to the output folder in storage account (dsOutFolderSA)
1. In previous pipeline add a filter activity after Get Metadata and remove for each
2. Add dynamic content and put below expression:
	@activity('Get Metadata1').output.childitems
2. Add a file on the VM where Self Hosted IR is setup. File name should start with - cars
3. Add a filter condition
	@startswith(item().name, 'c')
4. Debug pipeline and see the JSON output of the activities
5. Add For each loop and configure
	@activity('Filter1').output.Value
6. Now go into For Each activity and create Copy Data activity
	Source: dsFolderIn, Wildcard Path, @item().name
	Sink: dsOutFolderSA
	

- Refer: pipeline3
