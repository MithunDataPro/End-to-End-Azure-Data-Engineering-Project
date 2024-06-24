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

-- Assign roles/permissions (optional, example: db_datareader and db_datawriter)
ALTER ROLE db_datareader ADD MEMBER your_user_name;
ALTER ROLE db_datawriter ADD MEMBER your_user_name;


