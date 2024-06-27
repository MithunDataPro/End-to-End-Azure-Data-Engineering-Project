# End-to-End-Azure-Data-Engineering-Project
In this project we are going to create an end to end data platform right from Data Ingestion, Data Transformation, Data Loading and Reporting. 


# Project Overview- Architecture

![Project Architecture](https://github.com/MithunDataPro/End-to-End-Azure-Data-Engineering-Project/blob/main/Data%20Engineering%20End%20to%20End%20Project%20Architecture.png)

In this project, we are going to create an end-to-end data platform covering Data Ingestion, Data Transformation, Data Loading, and Reporting. 

## Tools Covered
1. Azure Data Factory
2. Azure Data Lake Storage Gen2
3. Azure Databricks
4. Azure Synapse Analytics
5. Azure Key Vault
6. Azure Active Directory (AAD)
7. Microsoft Power BI

## Use Case
The use case for this project involves building an end-to-end solution by ingesting tables from an on-premise SQL Server database using Azure Data Factory and storing the data in Azure Data Lake. Azure Databricks is used to transform the raw data into its cleanest form. We then use Azure Synapse Analytics to load the clean data and finally use Microsoft Power BI to integrate with Azure Synapse Analytics to build an interactive dashboard. Azure Active Directory (AAD) and Azure Key Vault are used for monitoring and governance purposes.

## Agenda
- **Part 1:** Environment setup
- **Part 2:** Data Ingestion
- **Part 3:** Data Transformation
- **Part 4:** Data Loading
- **Part 5:** Data Reporting
- **Part 6:** End-to-End Pipeline Testing


## Part 1: Environment Setup

Created Azure Data Factory & Downloaded AdventureWorks DB. Follow the link below to import the database used in this project to SSMS (I used the lightweight version):

[AdventureWorks Installation and Configuration](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

I added the lightweight version to MSSQL using Microsoft SSMS and ingested data from this database to Azure Data Lake Storage using Azure Data Factory. Additionally, to connect with Azure, I created a new user in MSSQL and gave them read access.

### Here is the code:

```sql
-- Create a login
CREATE LOGIN mithun WITH PASSWORD = '@Welcome2024#';

-- Create a user in the database and map to the login
USE AdventureWorksLT2017;
CREATE USER Mike FOR LOGIN mithun;

-- Assign roles/permissions
ALTER ROLE db_datareader ADD MEMBER Mike;
ALTER ROLE db_datawriter ADD MEMBER Mike;
```
So, now I have created a Key Vault in Azure to save my username and password due to its encryption for further usage!


## Part 2: Data Ingestion
I have completed part-1 data ingestion from On-premises SQL server to Azure Data Lake Storage Gen2 through Azure Data Factory.

#### Steps:
### - **1.**  Created Azure Data Factory:

- **a** Saved my username & password in Azure Key Vault for encryption.
- **b** Provided required permissions to the Admin.

### - **2.** Created a pipeline:

- **a** Created a Lookup activity in the source dataset.
- **b** Created a linked service and installed Microsoft Self-hosted Integration Runtime to connect with the on-premises database.
- **c** Provided the required on-premises SQL server name, database name, username & password from the vault, and checked the connections.
- **d** In the query box, used the following query to get all tables from the database:
- 
``` sql
Copy code
SELECT 
  s.name AS SchemaName,
  t.name AS TableName
FROM sys.tables t
INNER JOIN sys.schemas s ON t.schema_id = s.schema_id
WHERE s.name = 'SalesLT';
```
- **e** Debugged and published the query.

### - **3.** Created a Foreach activity:

- **a** Connected Lookup to Foreach.
- **b** In settings, selected items -> dynamic content -> @activity('look for all tables').output.value

Explained why we used this:

This command allows iterating through each table dynamically.
In activities -> For Each column, selected edit option and dragged Copy Data activity.

Provided source and sink. In the source, used the following query:

sql
Copy code
@{concat('SELECT * FROM ', item().SchemaName, '.', item().TableName)}
In user properties, added new properties SchemaName & TableName and provided values @item().SchemaName & @item().TableName.

Explained why we used this:

This dynamic query constructs the SELECT statement for each table and copies the data.
Imported data in Parquet format:

Explained why we use Parquet format:
Parquet is efficient for storage and retrieval, supporting advanced compression techniques and efficient encoding schemes.
In Parquet tables connection, selected linked service file path:
The container created in Data Storage has:
Bronze for raw data
Silver for semi-transformed data
Gold for cleaned data
Selecting bronze / @{concat(dataset().SchemaName, '/', dataset().TableName)} / @{concat(dataset().TableName, '.parquet')} to automate file extensions.
This completes Part-1 of the project.

