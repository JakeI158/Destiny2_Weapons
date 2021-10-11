# Exploring the Data
Time to explore the data in more detail! (pgAdmin - PostgreSQL)

## Element and Weapon Type
Let's say you are interested to see what guns are of the 'solar' element type.
```SQL
SELECT *
  FROM guns
 WHERE gun_element = 'Solar';
```

This returns 104 items. Next, let's take a look at the various scout rifles that are available (not necessarily of solar affinity).
```SQL
SELECT *
  FROM guns
 WHERE weapon_type = 'scout_rifle';
```

This returns 30 items. That's a bit cleaner than our previous query!<br />
Now I want to find out which scout rifles are of the solar element type.
```SQL
SELECT *
  FROM guns
 WHERE lower(gun_element) = 'solar'  --Note: This is where using PostgreSQL (and pgAdmin) starts to differ from MS SQL Server (T-SQL), to bypass the case sensitivity, we need to use lower().
   AND weapon_type = 'scout_rifle';
```

We could also use lower() like this:
```SQL
SELECT *
  FROM guns
 WHERE lower(gun_element) = 'solar'
   AND lower(weapon_type) = 'scout_rifle';
```

We now have a selection of 7 items to examine. Next, I want to take a look at the solar scouts that are of the precision archetype.
```SQL
SELECT *
  FROM guns
 WHERE lower(gun_element) = 'solar'
   AND lower(weapon_type) = 'scout_rifle'
   AND lower(gun_archetype) = 'precision';
```

This leaves us with 3 options: Oxygen SR3, Vision of Confluence, and Distant Relation.<br />
Say we now want to find out if any of these 3 weapons come from my favorite activity in the game, the Vault of Glass raid.
```SQL
SELECT *
  FROM guns
 WHERE lower(gun_element) = 'solar'
   AND lower(weapon_type) = 'scout_rifle'
   AND lower(gun_archetype) = 'precision'
   AND lower(gun_source) = 'vault of glass';
```

This leaves us with Vision of Confluence. I guess I'll be entering the Vault of Glass in the near future!

## Rate of Fire (RoF) and Rarity
Now I want to dig deeper into the rate of fire (RoF) options for hand cannons.<br />
First, I'll take a look at the hand cannons available and sort them from lowest to highest RoF.<br />
For now, all I need to know is the name and RoF.
```SQL
SELECT gun_name, gun_RoF
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
 ORDER BY gun_RoF;
```

This returns 44 hand cannons. That's a lot of options; maybe I want to view them alphabetically while keeping them in ascending order by RoF.
```SQL
SELECT gun_name, gun_RoF
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
 ORDER BY gun_RoF, gun_name;
```

Let's say I'm interested to see what is available with a RoF that is higher than 140.
```SQL
SELECT gun_name, gun_RoF
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
   AND gun_RoF > 140
 ORDER BY gun_RoF, gun_name;
```

We now have a selection of 11, but I also want to know which are of 'exotic' rarity and which are of 'legendary' rarity.
```SQL
SELECT gun_name, gun_RoF, rarity
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
   AND gun_RoF > 140
 ORDER BY gun_RoF, gun_name;
```

Let's go ahead and get a count of how many are exotic and how many are legendary.
```SQL
SELECT rarity, COUNT(rarity) AS Total
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
   AND gun_RoF > 140
 GROUP BY rarity;
```

This gives us 4 exotics and 7 legendaries.<br />
Finally, let's examine the 4 exotic hand cannons we are left with.
```SQL
SELECT *
  FROM guns
 WHERE lower(weapon_type) = 'hand_cannon'
   AND gun_RoF > 140
   AND lower(rarity) = 'exotic';
```

We are left with Sunshot, Malfeasance, The Last Word, and Crimson. Now we can make a decision on which weapon we'd like to use based on all of the above criteria!

## Archetypes
To continue our exploration of this data, I want to examine the various archetypes available for each weapon type.<br />
I'll start be determining what the different weapon types are, and how many of each are available.
```SQL
SELECT weapon_type, COUNT(weapon_type) AS Total
  FROM guns
 GROUP BY weapon_type;
```

Now let's get an overview of the archetypes.
```SQL
SELECT gun_archetype, COUNT(gun_archetype) AS Total
  FROM guns
 GROUP BY gun_archetype;
```

Now, this works as a general summary, but we can see there are duplicates where some archetypes are shared by different weapon types.
```SQL
SELECT gun_archetype, COUNT(gun_archetype) AS Total, weapon_type
  FROM guns
 GROUP BY gun_archetype, weapon_type
 ORDER BY gun_archetype;
```

Let's say we want to find the weapons that are hard-hitting. We'll want to explore our options in the 'High Impact' archetype further.<br />
Note: I need to specify both 'High Impact' and 'High-Impact' in the query as these are both used in the dataset.
```SQL
SELECT gun_archetype, COUNT(gun_archetype) AS Total, weapon_type
  FROM guns
 WHERE lower(gun_archetype) = 'high impact' or lower(gun_archetype) = 'high-impact'
 GROUP BY gun_archetype, weapon_type
 ORDER BY gun_archetype;
```

## Putting Together a Loadout
Now that we have this selection of weapons, I want to build a loadout from these options.<br />
We'll need a kinetic, energy (elemental), and heavy weapon for this.<br />
Let's expand the previous query to see these details.
```SQL
SELECT gun_archetype, weapon_type, gun_element
  FROM guns
 WHERE lower(gun_archetype) = 'high impact' or lower(gun_archetype) = 'high-impact'
 ORDER BY gun_element, weapon_type, gun_archetype;
```

Now to choose a kinetic weapon.
```SQL
SELECT gun_name, gun_archetype, weapon_type, gun_element
  FROM guns
 WHERE (lower(gun_archetype) = 'high impact' or lower(gun_archetype) = 'high-impact')
   AND lower(gun_element) = 'kinetic'
 ORDER BY gun_element, weapon_type, gun_archetype;
```

I'm a fan of the 'No Time to Explain' pulse rifle, so I'll choose that.<br />
Now for our energy/elemental choice.
```SQL
SELECT gun_name, gun_archetype, weapon_type, gun_element
  FROM guns
 WHERE (lower(gun_archetype) = 'high impact' or lower(gun_archetype) = 'high-impact')
   AND lower(gun_element) NOT LIKE 'kinetic'
 ORDER BY gun_element, weapon_type, gun_archetype;
```

I'll go ahead and choose the Glacioclasm void fusion rifle for my energy weapon.<br />
Finally, we'll need to choose a heavy weapon and we'll have our High Impact loadout selected!<br />
I'm a fan of explosions so let's choose from the available rocket launchers.
```SQL
SELECT gun_name, gun_archetype, weapon_type, gun_element
  FROM guns
 WHERE (lower(gun_archetype) = 'high impact' or lower(gun_archetype) = 'high-impact')
   AND lower(weapon_type) = 'rocket_launcher'
 ORDER BY gun_element, weapon_type, gun_archetype;
```

Code Duello is a good choice, so we'll select that. and that's it, we've selected the following loadout using the above queries:<br />
No Time to Explain, Glacioclasm, Code Duello<br />
To finish this set of queries, let's just take a look at all of the information available for new loadout.
```SQL
SELECT *
  FROM guns
 WHERE gun_name IN ('No Time to Explain', 'Glacioclasm', 'Code Duello');
```

Pretty cool!!

# More Cleanup
As I prepare the data for visualization, I've found that there are a few things that could be cleaned up further.

## Prepping for Visualization
Using the following query, I can see that the High Impact archetype shows up as both 'High Impact' and 'High-Impact' so I want to make them the same.
```SQL
SELECT DISTINCT gun_archetype, weapon_type
  FROM guns
 WHERE gun_archetype = 'High Impact' OR gun_archetype = 'High-Impact'
 ORDER BY gun_archetype;
```

To change all of the 'High Impact' weapons to 'High-Impact' we'll use the following:
```SQL
UPDATE guns
   SET gun_archetype = 'High-Impact'
 WHERE gun_archetype = 'High Impact';
```

Running this again, we'll see that the archetype was updated properly!
```SQL
SELECT DISTINCT gun_archetype, weapon_type
  FROM guns
 WHERE gun_archetype = 'High Impact' OR gun_archetype = 'High-Impact'
 ORDER BY gun_archetype;
```

To finish up, we'll do the same for Rapid Fire/Rapid-Fire.
```SQL
SELECT DISTINCT gun_archetype, weapon_type
  FROM guns
 WHERE gun_archetype = 'Rapid Fire' OR gun_archetype = 'Rapid-Fire'
 ORDER BY gun_archetype;
```

To update all relevant weapons to 'Rapid-Fire':
```SQL
UPDATE guns
   SET gun_archetype = 'Rapid-Fire'
 WHERE gun_archetype = 'Rapid Fire';
```

To check:
```SQL
SELECT DISTINCT gun_archetype, weapon_type
  FROM guns
 WHERE gun_archetype = 'Rapid Fire' OR gun_archetype = 'Rapid-Fire'
 ORDER BY gun_archetype;
```

Using the following, I'll check to make sure everything looks how I want:
```SQL
SELECT *
  FROM guns;
```

Now it's time to extract this updated dataset and begin the visualization!!
