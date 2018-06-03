# sp_GenMerge
SQL Server stored procedure to generate a merge statement with data.

Description: Quickly and efficiently generate a MERGE statement with data,
			 which will execute INSERT / UPDATE / DELETE on the basis of the source key from the source in the target table.
       
##### Typical use cases:
*	Generate scripts for static dictionaries, save the .sql file in the code repository and continue to use it for Dev / Test / Prod implementations
*	Generate data from the Prod environment to reproduce them in your Dev/Test environment.
*	Generated scripts can be modified and restarted to migrate changes between environments.
*	Enter the test data into the Dev environment and then generate the script,  
	so that you can always restore the test database with the correct sample data
  
The PrintMAX procedure by Ben Dill's was used: https://weblogs.asp.net/bdill/sql-server-print-max

If the data lines are not too long (<3500 characters), you can run the procedure with result to text.
If the lines are too large and SSMS crops the text, copy the print text and paste the data from the grid into the appropriate place.

##### Example of use:
#
```sql
sp_GenMerge 'Sales.Currency'
```

```sql
EXEC sp_GenMerge
     @source_table           = 'Person.Person'
   , @skip_columns           = 'AdditionalContactInfo, ModifiedDate'
   , @update_only_if_changed = 0`
```  

```sql
EXEC sp_GenMerge
     @source_table  = 'Production.ScrapReason'
   , @skip_identity = 0`
```   

```sql
EXEC sp_GenMerge
     @source_query  = 'SELECT ScrapReasonID, ''DEV_'' + Name AS Name, GETDATE() AS ModifiedDate  FROM Production.ScrapReason'
   , @target_table  = 'Production.ScrapReason'
   , @on_condition  = 'Target.ScrapReasonID = Source.ScrapReasonID'
   , @skip_computed = 0`
```

```sql
EXEC dbo.sp_GenMerge
     @source_table           = N'Production.ScrapReason'  
   , @target_table           = NULL
   , @source_query           = NULL
   , @target_query           = NULL
   , @on_condition           = NULL
   , @skip_columns           = NULL
   , @skip_insert            = 0
   , @skip_update            = 0
   , @skip_delete            = 1
   , @skip_identity          = 1
   , @skip_computed          = 0
   , @skip_timestamp         = 0
   , @skip_images            = 0
   , @skip_data              = 0
   , @update_only_if_changed = 1
   , @debug                  = 0
   ```
