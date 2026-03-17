You are absolutely right. Since you have multiple tables, you must keep the dynamic item().TABLE_NAME logic. Using a static script like TRUNCATE TABLE [dbo].[YourTableName] inside a loop would disastrously try to truncate the same table over and over (or fail) for every iteration. 
Yes, TRUNCATE is the right approach for a daily full sync because it preserves your table structure (indexes, permissions, keys), whereas DROP deletes them and forces the "Auto Create Table" to rebuild a basic heap table every day (killing performance). 
The Correct Dynamic Script
Replace your DROP script with this dynamic TRUNCATE script:
sql
@{concat('TRUNCATE TABLE [', item().TABLE_SCHEMA, '].[', item().TABLE_NAME, '];')}
Use code with caution.

Why this is the best approach
Dynamic: The @{concat(...)} function builds a specific SQL command for each table in your loop (e.g., TRUNCATE TABLE [dbo].[Customers]; then TRUNCATE TABLE [dbo].[Orders];).
Performance: TRUNCATE is instant and minimally logged, whereas DELETE or DROP + CREATE takes longer.
Stability: It leaves your "container" (the table) alone and just empties the content. This means any custom indexes or user permissions you added to the database will remain intact.
⚠️ Important Safety Check
TRUNCATE will fail if the table does not exist yet. 
Scenario A: All tables already exist (since your sync is working). -> Use the script above.
Scenario B: You expect new tables to appear in the source periodically and want ADF to auto-create them. -> Use the "Safe" version below.
The "Safe" Truncate (Checks if table exists first):
sql
@{concat('IF OBJECT_ID(''[', item().TABLE_SCHEMA, '].[', item().TABLE_NAME, ']'', ''U'') IS NOT NULL TRUNCATE TABLE [', item().TABLE_SCHEMA, '].[', item().TABLE_NAME, '];')}
Use code with caution.

(Note: The double single-quotes '' are necessary to escape the string correctly inside the SQL command.)
Recommendation: Stick to Scenario A (Simple Dynamic Truncate) unless you are frequently adding brand new tables.
