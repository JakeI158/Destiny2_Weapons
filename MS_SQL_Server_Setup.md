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
	gun_element varchar(7),
	gun_RoF integer,
	rarity varchar(10),
	weapon_type varchar(20)
);
```

## Insert Data into Table
```SQL
BULK INSERT guns
FROM 'C:\Users\your_name\Desktop\guns.csv' --This will vary depending on where you save the dataset
WITH
(
	FIRSTROW = 2, --First row is technically the csv header
	FIELDTERMINATOR = ',', --CSV field delimiter
	ROWTERMINATOR = '\n', --Use to shift the control to the next row
	TABLOCK
);
```

# Initial Data Cleanup
## Errors
Upon inserting the original dataset into the newly created table, I encountered these errors:

Recieved error:
Msg 4863, Level 16, State 1, Line 12
Bulk load data conversion error (truncation) for row 296, column 4 (gun_element).
Msg 4863, Level 16, State 1, Line 12
Bulk load data conversion error (truncation) for row 376, column 4 (gun_element).

To fix this, I found what I thought the missing rows and used the following to insert them into the table:
```SQL
INSERT INTO guns
VALUES ('Good Bone Structure', 'Precision Slug', 'Gunsmith', 'Arc', 64, 'Legendary', 'shotgun');
```

As it turns out, I misinterpreted the errors, but luckily I only added one wrong line. To resolve this, I removed the line using:
```SQL
DELETE FROM guns
 WHERE gun_name = 'Good Bone Structure';
```

Now, this did delete the correct (original entry of this weapon) row as well, so we'll need to re-insert that weapon's data:
```SQL
INSERT INTO guns
VALUES ('Good Bone Structure', 'Precision Slug', 'Gunsmith', 'Arc', 64, 'Legendary', 'shotgun');
```

Now that I've figured out the proper rows that got missed, I can add these to the table:
```SQL
INSERT INTO guns
VALUES ('Two-Tailed Fox', 'Exotic', 'World', 'VoidSol', 40, 'Exotic', 'rocket_launcher');
```
Note: This one originally failed due to the length of the gun_element info, Void/Solar was too long so I shortened to VoidSol to fit the requirement

```SQL
INSERT INTO guns
VALUES ('First In, Last Out', 'Precision Slug', 'World Engram', 'Arc', 64, 'Legendary', 'shotgun');
```
Note: This one originally failed due to the gun_name inherently containing a comma, to fix this all I needed to do was manually insert the row via the above SQL statement

## Cleanup Check
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

Now we can starting exploring the data with SQL queries and learn more!
