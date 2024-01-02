# SQL-Data-Validation-Cleaning

**Project Title:** Employee Data Management System

**Project Description:**
The Employee Data Management System is a comprehensive project aimed at organizing and managing essential employee information. The dataset encompasses key details, including unique Employee ID, Name, Gender, Department, Salary, Full-Time Equivalent (FTE), and Work Location.

**Key Features:**
1. **Comprehensive Employee Profiles:** The system captures detailed profiles of each employee, including demographic details like Name, Gender, and specific employment-related information such as Department and Salary.
2. **Location Flexibility:** The project accounts for the diverse work locations of employees, ranging from on-site locations such as Seattle, USA, and Wellington, New Zealand, to remote work arrangements in various cities globally.
3. **Workload Metrics:** The Full-Time Equivalent (FTE) field allows for the assessment of employee workload, with values ranging from 0.7 to 1.
4. **Departmental Distribution:** Employees are categorized into different departments, such as Business Development, Services, Training, Engineering, Support, Marketing, and Research and Development, reflecting the diverse functions within the organization.
5. **Currency-Agnostic Salary Information:** The system handles salary information, presented in a consistent format, making it easy to analyze and compare across employees.

**Project Implementation:**
The project leverages SQL for data validation, cleaning, and analysis. Queries and scripts are employed to ensure data integrity, validate formats, and perform consistency checks.

**Next Steps:**
1. Implement data validation checks to ensure data accuracy and integrity.
2. Clean data and prepare for providing insights by systematically addressing inconsistencies and inaccuracies.
3. Optimize the dataset for analysis using normalization and transformation techniques.

**Attachment:**
- Old Excel File (Before Data Cleaning).xlsx
  
	<img width="697" alt="Old" src="https://github.com/AdnanTheResearchScientist/SQL-Data-Validation-Cleaning/assets/152249280/3ff3a20a-fe85-406f-b603-59796a69760a">
  
- [New Excel File (After Data Cleaning).xlsx](https://github.com/AdnanTheResearchScientist/SQL-Data-Validation-Cleaning/files/13813366/New.Excel.File.After.Data.Cleaning.xlsx)
 
	<img width="782" alt="New" src="https://github.com/AdnanTheResearchScientist/SQL-Data-Validation-Cleaning/assets/152249280/36cb8333-a17a-4987-bb6a-aafe5cce606e">

This systematic process benefits analysts, data scientists, and decision-makers by providing reliable and actionable insights for informed decision-making. The Employee Data Management System is designed to be adaptable, allowing ongoing updates and enhancements to meet the evolving needs of the organization's human resources management.

```sql
-- Step 1: Selecting Data from 'DirtyData$' Table
SELECT TOP (1000) [Emp ID]
      ,[Name]
      ,[Gender]
      ,[Department]
      ,[Salary]
      ,[Start Date]
      ,[FTE]
      ,[Employee type]
      ,[Work location]
FROM [Jan1].[dbo].['DirtyData$'];

-- Step 2: Checking for NULL values
SELECT
  COUNT(*) AS NullCount_EmpID,
  COUNT(*) AS NullCount_Name,
  COUNT(*) AS NullCount_Gender,
  COUNT(*) AS NullCount_Department,
  COUNT(*) AS NullCount_Salary,
  COUNT(*) AS NullCount_FTE,
  COUNT(*) AS NullCount_WorkLocation
FROM [Jan1].[dbo].['DirtyData$']
WHERE
  [Emp ID] IS NULL OR
  [Name] IS NULL OR
  [Gender] IS NULL OR
  [Department] IS NULL OR
  [Salary] IS NULL OR
  [FTE] IS NULL OR
  [Work location] IS NULL;

-- Step 3: Checking for unique values in each column
SELECT
  COUNT(DISTINCT [Emp ID]) AS UniqueCount_EmpID,
  COUNT(DISTINCT [Name]) AS UniqueCount_Name,
  COUNT(DISTINCT [Gender]) AS UniqueCount_Gender,
  COUNT(DISTINCT [Department]) AS UniqueCount_Department,
  COUNT(DISTINCT [Salary]) AS UniqueCount_Salary,
  COUNT(DISTINCT [FTE]) AS UniqueCount_FTE,
  COUNT(DISTINCT [Work location]) AS UniqueCount_WorkLocation
FROM [Jan1].[dbo].['DirtyData$'];

-- Step 4: Checking Salary Metrics
SELECT
  MAX([Salary]) AS Max_Salary,
  MIN([Salary]) AS Min_Salary,
  AVG([Salary]) AS Avg_Salary,
  STDEV([Salary]) AS Stdev_Salary
FROM [Jan1].[dbo].['DirtyData$'];

-- Step 5: Data Cleaning - Separating CostCenter and EmployeeID in Emp ID column
ALTER TABLE [Jan1].[dbo].['DirtyData$']
ADD CostCenter NVARCHAR(10),
    EmployeeID NVARCHAR(50);

UPDATE [Jan1].[dbo].['DirtyData$']
SET 
    CostCenter = SUBSTRING([Emp ID], 1, 2),
    EmployeeID = SUBSTRING([Emp ID], 3, LEN([Emp ID]) - 2);

-- Step 6: Removing extra space in the Name column
UPDATE [Jan1].[dbo].['DirtyData$']
SET [Name] = LTRIM(RTRIM(REPLACE(REPLACE([Name], '  ', ' '), '  ', ' ')))
WHERE CHARINDEX('  ', [Name]) > 0;

-- Step 7: Showing inconsistent format in the Name column
SELECT [Name]
FROM [Jan1].[dbo].['DirtyData$']
WHERE 
    CHARINDEX('  ', [Name]) > 0 OR 
    [Name] LIKE ' %' OR [Name] LIKE '% ' OR 
    LEN([Name]) - LEN(REPLACE([Name], ' ', '')) > 1;

-- Step 8: Fixing inconsistency and choosing one format for the Name column
UPDATE [Jan1].[dbo].['DirtyData$']
SET [Name] = LTRIM(RTRIM(REPLACE(REPLACE([Name], '  ', ' '), '  ', ' ')));

-- Step 9: Rechecking for inconsistent format in the Name column
SELECT [Name]
FROM [Jan1].[dbo].['DirtyData$']
WHERE 
    CHARINDEX('  ', [Name]) > 0 OR 
    [Name] LIKE ' %' OR [Name] LIKE '% ' OR 
    LEN([Name]) - LEN(REPLACE([Name], ' ', '')) > 1;

-- Step 10: Updating Name for Emp ID 'SQ04934' and preparing for parsing, preserving "St."
UPDATE [Jan1].[dbo].['DirtyData$']
SET [Name] = LTRIM(RTRIM(REPLACE(REPLACE([Name], '  ', ' '), '  ', ' ')))
WHERE [Emp ID] = 'SQ04934';

-- Step 11: Updating Name for Emp ID 'SQ04934' and removing "St."
UPDATE [Jan1].[dbo].['DirtyData$']
SET [Name] = REPLACE([Name], 'St.', '')
WHERE CHARINDEX('St.', [Name]) > 0;

-- Step 12: Checking for the updated data for Emp ID 'SQ04934'
SELECT *
FROM [Jan1].[dbo].['DirtyData$']
WHERE [Emp ID] = 'SQ04934';

-- Step 13: Separating

 the Name column into First Name and Last Name
ALTER TABLE [Jan1].[dbo].['DirtyData$']
ADD FirstName NVARCHAR(50),
    LastName NVARCHAR(50);

-- Step 14: Updating the new columns based on the existing Name column
UPDATE [Jan1].[dbo].['DirtyData$']
SET 
    FirstName = LTRIM(RTRIM(SUBSTRING([Name], 1, CHARINDEX(' ', [Name] + ' ') - 1))),
    LastName = LTRIM(RTRIM(SUBSTRING([Name], CHARINDEX(' ', [Name] + ' ') + 1, LEN([Name]))));

-- Step 15: Deleting rows where either FirstName or LastName is null
DELETE FROM [Jan1].[dbo].['DirtyData$']
WHERE FirstName IS NULL OR LastName IS NULL;

-- Step 16: Adding new columns for City and Country
ALTER TABLE [Jan1].[dbo].['DirtyData$']
ADD City NVARCHAR(255),
    Country NVARCHAR(255);

-- Step 17: Updating the new columns based on the existing Work Location column
UPDATE [Jan1].[dbo].['DirtyData$']
SET 
    City = CASE WHEN [Work Location] = 'Remote' THEN 'Remote' ELSE PARSENAME(REPLACE([Work Location], ',', '.'), 2) END,
    Country = CASE WHEN [Work Location] = 'Remote' THEN 'Remote' ELSE PARSENAME(REPLACE([Work Location], ',', '.'), 1) END;

-- Step 18: Updating the Gender column, coding null values as 'Other'
UPDATE [Jan1].[dbo].['DirtyData$']
SET Gender = COALESCE(Gender, 'Other')
WHERE Gender IS NULL;

-- Step 19: Adding a new column for Full/Part
ALTER TABLE [Jan1].[dbo].['DirtyData$']
ADD FullPart NVARCHAR(10);

-- Step 20: Updating the new column based on the existing FTE column
UPDATE [Jan1].[dbo].['DirtyData$']
SET 
    FullPart = CASE WHEN FTE >= 1 THEN 'Full Time' ELSE 'Part Time' END;

-- Step 21: Creating a temporary table with a row number for each group of duplicates
WITH CTE AS (
    SELECT
        *,
        ROW_NUMBER() OVER (PARTITION BY CostCenter, EmployeeID ORDER BY FirstName) AS RowNum
    FROM
        [Jan1].[dbo].['DirtyData$']
)

-- Step 22: Deleting duplicates based on the row number
DELETE FROM CTE
WHERE RowNum > 1;

-- Step 23: Updating Department for null values
UPDATE [Jan1].[dbo].['DirtyData$']
SET Department = 'Other'
WHERE Department IS NULL;

-- Step 24: Updating Salary to the ceiling value
UPDATE [Jan1].[dbo].['DirtyData$']
SET Salary = CEILING(Salary);

-- Step 25: Creating a new table with the desired column order
CREATE TABLE [Jan1].[dbo].[NewDirtyData]
(
    CostCenter NVARCHAR(10),
	EmployeeID NVARCHAR(50),
    FirstName  NVARCHAR(255),
	LastName  NVARCHAR(255),
	City NVARCHAR(255),
	Country NVARCHAR(255),
	FullPart  NVARCHAR(255),
    Department NVARCHAR(255),
    Salary DECIMAL(10,2)
);

-- Step 26: Inserting data into the new table with the desired column order
INSERT INTO [Jan1].[dbo].[NewDirtyData] (CostCenter, EmployeeID, FirstName, LastName, City, Country, FullPart, Department, Salary)
SELECT CostCenter, EmployeeID, FirstName, LastName, Employeetype, City, Country, FullPart, Department, Salary
FROM [Jan1].[dbo].['DirtyData$'];

-- Step 27: Dropping the original table
DROP TABLE [Jan1].[dbo].['DirtyData$'];
```
