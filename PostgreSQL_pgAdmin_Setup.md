# Setup
## Create Database
```SQL
CREATE DATABASE Destiny2_Weapons;
```

## Create Table
```SQL
CREATE TABLE guns
(
	gun_name varchar(100),
	gun_archetype varchar(100),
	gun_source varchar(100),
	gun_element varchar(10),
	gun_RoF integer,
	rarity varchar(10),
	weapon_type varchar(20)
);
```

## Insert/Copy Data into Table
```SQL
\copy guns FROM 'C:\Users\your_name\Desktop\guns.csv' DELIMITER ',' CSV HEADER;
--The file path will vary depending on where you save the dataset
```

To prevent a similar issue to what I encountered when importing the data to MS SQL Server Management Studio (SSMS), I changed the character limit to 10 for the gun_element column.<br />
This also allowed the element to remain 'Void/Solar' as it was in the source, rather than 'VoidSol' as used in SSMS.

## Check the Imported Data
```SQL
SELECT *
  FROM guns;
```

Now this shows all 409 rows/unique weapon entries that we needed!

Just to double check, I ran the following to make sure there were no duplicates:
```SQL
SELECT DISTINCT *
  FROM guns;
```

```SQL
SELECT COUNT(*)
  FROM guns;
```

Now we can starting exploring the data with SQL queries and learn more!
