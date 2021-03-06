# onebenthicdb
SQL code from Vertabelo to create OneBenthicDB

# BACKUP THE DB
RMC on database in PGAdmin4>Backup...>Enter filepath and filename 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\BACKUP\OneBenthic'>Press Backup button.

# CREATE A COPY OF DB FOR TESTING/RESTORE
Create DB in PGAmin4>RMC on db, select 'Restore'> select file 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\BACKUP\OneBenthic'> Press Restore button.

# CHANGES TO DB STRUCTURE (IN PG ADMIN)
Table 'taxasample' col 'taxa_taxaqual_qualifier' changed from Not NULL? Yes to Not NULL? No

# USEFUL CODE FOR QUERYING
Use LIKE and % where items begings with
select * from sample where samplecode LIKE 'RSMP_SC%'

To delete/update records within a column use:
UPDATE taxasample SET taxa_taxaqual_qualifier = NULL 
WHERE taxa_taxaqual_qualifier = 'A';
This code gets rid of A's in col 'taxa_taxaqual_qualifier' in table 'taxasample'

To delete rows from table 'envvarsample' where col 'value' is [null] use:
DELETE FROM envvarsample where value IS NULL

# DATA ISSUES WHICH NEED TO BE RESOLVED
1. SC RSMP FAUNAL SAMPLES: PSA FOR <63um is split across all sieves. should be a total under 0mm siever. Has been resolved for PSA only samples. Problem also for new Baseline East Channel benthic samples. ISSUE RESOLVED 11/09/2019
2. Add sediment only samples from East Channel baseline (2014/15), BC and NW. I checked and there re no sed only samples for the east channel.
3. A_2018 survey macroprocessing lab should be 7 (not applicable) for samples where no fauna collected. At present all stations set to 2.

# WHERE ARE WE
SC data entered into table 'sample'

SC data entered into table 'survey'

SC data entered into table 'surveysample'

SC data entered into table 'samplestation'

SC data entered into table 'sampleowner'

SC data entered into table 'sedvarsample'

# DATA IMPORT PROCEDURES
## POPULATE TABLES ####
### ADD DATA FROM CSV FILE TO TABLE ####
COPY taxasample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\tables\table_taxasample.csv' WITH (FORMAT CSV, DELIMITER ',',NULL 'NA', HEADER);

nb/ make sure permission given for everyone and file in .csv format

### DELETE ALL DATA FROM TABLE
DELETE FROM tablename

## BRING IN NEW DATA
1. Start with 'sample' table

### ADD DATA TO sample TABLE LINE BY LINE
Notes:
Date needs to be in format:'2014-09-27',
Missing info as NULL, 
If no macro data use macroprocessinglab_id = 7 (no macro).


INSERT INTO sample (
id,
samplecode,	
samplelat,	
samplelong,	
waterdepth,	
year,	
month,	
date,	
grabsamplesize,	
macrosieve,
samplecode2,
psasubsample,	
samplenotes,	
treatment,	
inccol,	
samplephotowhole,	
samplephotoresidue,	
gear_gearcode,	
baselinesample,	
psaprocessinglab_id,	
macroprocessinglab_id)

VALUES(
33199,
'RSMP_H_0001_Baseline',
53.394505,
0.528082,
13,
2014,
9,
'2014-09-27,
5,
1,
NULL,
TRUE,
NULL,
NULL,
NULL,
NULL,
NULL,
'MHN',
FALSE,
2,
7);

### REMOVE RECORD FROM TABLE LINE BY LINE ####
DELETE FROM sample
WHERE samplecode = 'RSMP_H_0001_Baseline';

2. Enter sed data into table 'sedvarsample'

NB/ only enter data where sieve has been used (i.e. ignore NAs/NULL)

INSERT INTO sedvarsample (id,sample_samplecode,sedvar_sievesize,percentage) VALUES

(606677,'RSMP_H_0001_Baseline',	'63',	0),
(606678,'RSMP_H_0001_Baseline',	'45',	0),
(606679,'RSMP_H_0001_Baseline',	'31.5',	0),
(606680,'RSMP_H_0001_Baseline',	'22.4',	1.300148588),
(606681,'RSMP_H_0001_Baseline',	'16',	4.439078752),
(606682,'RSMP_H_0001_Baseline',	'11.2',	5.206785537),
(606683,'RSMP_H_0001_Baseline',	'8',	6.141654284),
(606684,'RSMP_H_0001_Baseline',	'5.6',	6.773155027),
(606685,'RSMP_H_0001_Baseline',	'4',	6.129271917),
(606686,'RSMP_H_0001_Baseline',	'2.8',	7.317979198),
(606687,'RSMP_H_0001_Baseline',	'2',	4.84769688),
(606688,'RSMP_H_0001_Baseline',	'1.4',	3.293709757),
(606689,'RSMP_H_0001_Baseline',	'1',	2.526002972),
(606690,'RSMP_H_0001_Baseline',	'0.71',	1.931649331),
(606691,'RSMP_H_0001_Baseline',	'0.5',	2.40837048),
(606692,'RSMP_H_0001_Baseline',	'0.355',	3.436106984),
(606693,'RSMP_H_0001_Baseline',	'0.25',	12.22758791),
(606694,'RSMP_H_0001_Baseline',	'0.18',	18.07206538),
(606695,'RSMP_H_0001_Baseline',	'0.125',	7.75136206),
(606696,'RSMP_H_0001_Baseline',	'0.09',	1.312530956),
(606697,'RSMP_H_0001_Baseline',	'0.063',	0.755324418),
(606698,'RSMP_H_0001_Baseline',	'0',	4.129519564);


### ADD DATA TO sedvarsample TABLE USING CSV IMPORT
NB Last id used was 682113 (after import). Get sed data into wide format. Column headers: samplecode (e.g. 'RSMP_A_0001_Mon_2018' - col 1), samplecode (col2), samplecode (col3). Rows: A sed fraction (e.g. 63, 45, 31.5 ... NB/ ensure headers in mm -see table'sedvar') follows by percentage values by sample. Save file (e.g. A2018psashort.csv. Next change from wide (short) to long format using FILE 'get_psa_data_into_long_format.R'. Save long format data and import to table 'sedvarsample'. Dont forget about file permissions.

COPY sedvarsample
FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sedvarsample humber baseline psa only.csv' DELIMITER ','  CSV HEADER;

### ADD DATA TO sample TABLE USING CSV IMPORT
NB Last id used was 34348
COPY sample
FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sample humber baseline psa only.csv' DELIMITER ','NULL AS '[null]'  CSV HEADER;

ADD DATA TO survey TABLE
NB you can’t add data into table surveysample until there survey is there
INSERT INTO survey (surveyname, programme, surveypurpose, datapubliclyavailable, data)VALUES('Anglian Regional Seabed Monitoring Programme 2018','RSMP','Monitoring',FALSE,'N')

3. Enter data into table 'surveysample'
### ADD DATA to table surveysample
NB Last id used was 35158 (after import)
COPY surveysample
FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update surveysample humber baseline psa only.csv' DELIMITER ',' CSV HEADER;

Make sure any new stations are added:
INSERT INTO station(stationcode,stationlong,stationlat,stationnotes,stationsubgroup2,stationsubgroup1,	stationgroup) VALUES
('A_0870',2.286766, 52.42388,NULL,NULL,'Anglian RSMP','RSMP'),
('A_0871',2.255853,52.483941,NULL,NULL,'Anglian RSMP','RSMP'),
('A_0872',1.990628,52.787012,NULL,NULL,'Anglian RSMP','RSMP'),
('A_0873',2.130847,52.202034,NULL,NULL,'Anglian RSMP','RSMP');


4. Enter data into table 'samplestation'
NB Last id used was 6069 (after import)
COPY samplestation FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update samplestation humber baseline psa only.csv' DELIMITER ',' CSV HEADER;

5. Enter data into table 'sampleowner'
NB Last id used was 35708 (after import)
COPY sampleowner FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sampleowner humber baseline psa only.csv' DELIMITER ',' CSV HEADER;

## TO REPLACE ERRONEOUS RECORDS FROM TABLE sedvarsample

# First remove incorrect records using code:
DELETE FROM sedvarsample
WHERE 
sample_samplecode = 'RSMP_EC_0001_Baseline' OR
sample_samplecode = 'RSMP_EC_0002_Baseline' OR
sample_samplecode = 'RSMP_EC_0003_Baseline';

then add correct data in normal way. Eg.
COPY sedvarsample
FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sedvarsample humber baseline psa only.csv' DELIMITER ','  CSV HEADER;

6. Enter data into table 'taxasample'
Start by getting taxon abundance matrix into correct format:
- Ensure samplecodes match those in the sample table
- Rename col 1 'samplecode' This col contains the taxon names (this is not a mistake)
- Rename col 2 'qualifier'
- Replace 'P' with '1'
- Insert 'NA' to blank cells
Save file as A2018benshort.csv
Use 'get_abund_data_into_long_format.R' to change data into long format. This results in a file 'A2018abundlong.csv'
Open 'A2018abundlong.csv' and insert col for 'id' and 'biomass'. If no biomass data enter [null]. NB Last id used was 1225216 (1228861) (after import) 

COPY taxasample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\A2018abundlong.csv' DELIMITER ',' CSV HEADER;



NB/ If any taxa are missing from table'taxa' then these will need to be entered manually using code:
INSERT INTO faunal_data.taxa(taxonname,countable,aphiaID,matchtype,lsid,tsn,qualitystatus,taxonstatus,scientificname,authority,aphiaidaccepted,scientificnameaccepted,authorityaccepted,kingdom,phylum,"class","order","family",genus,subgenus,species,subspecies,ismarine,isbrackish,isfresh,isterrestrial,citation,taxonnotes,include,nonnative,conservationimportance,taxaqual_qualifier)
VALUES('Aoridae (female)',true,101368,'exact','urn:lsid:marinespecies.org:taxname:101368',93440,'Checked by Taxonomic Editor','accepted','Aoridae','Stebbing, 1899',101368,'Aoridae','Stebbing, 1899','Animalia','Arthropoda','Malacostraca','Amphipoda','Aoridae','Aoridae','Aoridae',NULL,NULL,true,NULL,NULL,NULL,'Horton, T.; De Broyer, C. (2015). Aoridae Stebbing, 1899. In: Horton, T.; Lowry, J.; De Broyer, C.; Bellan-Santini, D.; Coleman, C. O.; Daneliya, M.; Dauvin, J-C.; Fiser, C.; Gasca, R.; Grabowski, M.; Guerra-Garcia, J. M.; Hendrycks, E.; Holsinger, J.; Hughes, L.; Jazdzewski, K.; Just, J.; Kamaltynov, R. M.; Kim, Y.-H.; King, R.; Krapp-Schickel, T.; LeCroy, S.; Lorz, A.-N.; Senna, A. R.; Serejo, C.; Sket, B.; Thomas, J.; Thurston, M.; Vader, W.; Vainola, R.; Vonk, R.; White, K.; Zeidler, W. World Amphipoda Database. Accessed through:  World Register of Marine Species at http://www.marinespecies.org/aphia.php?p=taxdetails&id=101368 on 2015-10-06',NULL,true,false,false,'F')



# QUERING MULTIPLE TABLES (INNER JOIN)
https://www.youtube.com/watch?v=7h9uuILngp0

select samplecode,samplelat, samplelong,station_stationcode,stationlong,stationlat,baselinefaunalcluster_faunalcluster
FROM sample, samplestation,station,cluster
where sample.samplecode=samplestation.sample_samplecode
AND samplestation.station_stationcode=station.stationcode
AND sample.samplecode=cluster.sample_samplecode
and station_stationcode like 'SC_%'
ORDER BY station_stationcode;

## Output abund data at family level (OneBenthic on KC's PC)
SELECT sample_samplecode,family,abund,samplelat,samplelong
FROM taxasample,faunal_data.taxa ,sample
WHERE
 taxasample.taxa_taxonname=faunal_data.taxa.taxonname
AND
taxasample.taxa_taxaqual_qualifier=faunal_data.taxa.taxaqual_qualifier
AND sample_samplecode like 'RSMP%_2018'
include=true AND
AND sample.samplecode = taxasample.sample_samplecode
ORDER BY sample_samplecode;

## Output abund data at family level (from OneBenthic on azsclnxgis-ext01)
SELECT 
su.surveyname,
s.samplecode,
s.samplelat,
s.samplelong,
w.validname,
w.family,
ts.abund 
FROM associations.survey as su 
INNER JOIN associations.surveysample as ss ON ss.survey_surveyname = su.surveyname 
INNER JOIN samples.sample as s ON ss.sample_samplecode = s.samplecode 
INNER JOIN faunal_data.taxasample as ts ON s.samplecode= ts.sample_samplecode 
INNER JOIN faunal_data.worrmstaxa as wt ON wt.taxonname = ts.worrmstaxa_taxonname 
INNER JOIN faunal_data.aphia as a ON wt.aphia_aphiaid = a.aphiaid 
INNER JOIN faunal_data.worrms as w ON w.validaphiaid = a.worrms_validaphiaid 
WHERE su.surveyname = 'Area 222 2011' ORDER by s.samplecode

## Output abund data at 'scientificnameaccepted' level
SELECT sample_samplecode,scientificnameaccepted,abund,samplelat,samplelong
FROM taxasample,faunal_data.taxa ,sample
WHERE
 taxasample.taxa_taxonname=faunal_data.taxa.taxonname
AND
taxasample.taxa_taxaqual_qualifier=faunal_data.taxa.taxaqual_qualifier
AND sample_samplecode like 'RSMP%_2018'
AND sample.samplecode = taxasample.sample_samplecode
ORDER BY sample_samplecode;


7. Enter data into table 'cluster'. This is the csv output from the Faunal Cluster ID tool. Output file first needs converting into the correct format using R script 'get_fam_abund_into_format_for_faunalpredictIDapp.R'. Make sure file permission set to Everyone. Code for bringing in file:
COPY cluster FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\clusterinput.csv' DELIMITER ','NULL AS '[null]' CSV HEADER;

# Need to add new env variables to table 'envar'?
INSERT INTO envvar (name,units) 
VALUES ('ww_biomass_cnidaria','g'), 
('ww_biomass_polychaeta','g'),
('ww_biomass_oligochaeta','g'),
('ww_biomass_other','g'); 

8. Enter data into table 'envvarsample'. This is the csv biomass data (mjr taxonomic groups). Output file first needs converting into the correct format using R script 'get_fam_abund_into_format_for_faunalpredictIDapp.R' see section 'FORMAT CSV OF BIOMASS DATA FOR ENTRY INTO TABLE 'envvarsample'. Make sure file permission set to Everyone. Code for bringing in file:

COPY envvarsample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\data4envvarsample.csv' DELIMITER ','NULL AS '[null]' CSV HEADER;

# STORING IMAGES
Advice on storing images in postgres (https://supun-biz.blogspot.com/2011/03/easy-way-to-import-image-to-bytea-field.html)
Convert jpeg to base64 (https://www.motobit.com/util/base64-decoder-encoder.asp)
Convert base64 to jpeg (https://codebeautify.org/base64-to-image-converter)

In postgres create a table called images then add 2 columns: image_name (type chr), image_data (type:btea)

Copy base64 code and inset into the following query:
INSERT INTO images (image_name, image_data)
VALUES( 'image_one', decode('insert base64 code here', 'base64') );

That's it!


## MANAGING DATABASE THROUGH COMMAND PROMPT
Go to CMD. To gain access to console psql type: 

psql -h azsclnxgis-ext01.postgres.database.azure.com -U kmc00_benthic_editor@azsclnxgis-ext01 -d one_benthic
Use this one from 13/01/2020:
psql -h azsclnxgis-ext01.postgres.database.azure.com -U editors_one_benthic@azsclnxgis-ext01 -d one_benthic

For VM first type:
cd "C:\Program Files\PostgreSQL\12\bin>"

Add PW: inv........

Once in the datbase console : 

\d	to see tables
\dn	to see list of schemas
SELECT * FROM samples.sample limit 10;
q	to escape from a query

# To insert data into table 'sample'
\COPY samples.sample (id, samplecode, samplelat, samplelong, waterdepth, year, month, date, grabsamplesize, macrosieve, samplecode2, psasubsample, samplenotes, treatment, inccol, samplephotowhole, samplephotoresidue, gear_gearcode, baselinesample, psaprocessinglab_id, macroprocessinglab_id) FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sample MD99.csv' DELIMITER ','NULL AS '[null]' CSV HEADER;

or

\COPY samples.sample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sample 222 2011.csv' DELIMITER ',' CSV HEADER; 

Note that date in format MM/DD/YYYY (08/11/2019). Make sure cols: geom, in_mpa and mpa_id are left blank. Null cols should be left blank



# To insert data into table 'survey'
Do this in PG Admin (doesn't work in command line)
INSERT INTO associations.survey (surveyname, programme, surveypurpose, datapubliclyavailable, data)VALUES('Median Deep 1999','EIA','Characterisation',false,'E');

# To insert data into table 'surveysample'
\COPY associations.surveysample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update surveysample MD99.csv' DELIMITER ',' CSV HEADER;

# To insert data into table 'sampleowner'
\COPY associations.sampleowner FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sampleowner MD99.csv' DELIMITER ',' CSV HEADER;

# To insert data into table 'sedvarsample'
\COPY sediment_data.sedvarsample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\table update sedvarsample MD99.csv' DELIMITER ',' CSV HEADER;

# To insert data into table 'faunal_data.taxasample'
\copy faunal_data.taxasample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\SC2017_18benlong.csv' DELIMITER ',' CSV HEADER;

# To see data in table 'faunal_data.taxa'
SELECT * FROM faunal_data.taxa where taxonname like 'PORIFERA%';

# To insert missing taxa into table 'faunal_data.taxa'

Can do this in pgadmin

INSERT INTO faunal_data.taxa(taxonname,countable,aphiaID,matchtype,lsid,tsn,qualitystatus,taxonstatus,scientificname,authority,aphiaidaccepted,scientificnameaccepted,authorityaccepted,kingdom,phylum,"class","order","family",genus,subgenus,species,subspecies,ismarine,isbrackish,isfresh,isterrestrial,citation,taxonnotes,include,nonnative,conservationimportance,taxaqual_qualifier) VALUES('Gastropoda eggs',true,101,'exact','urn:lsid:marinespecies.org:taxname:101',69459,'Checked by Taxonomic Editor','accepted','Gastropoda','Cuvier, 1795',101,'Gastropoda','Cuvier, 1795','Animalia','Mollusca','Gastropoda','Gastropoda','Gastropoda','Gastropoda',NULL,NULL,NULL,true,true,true,true,'Gofas, S. (2009). Gastropoda. Accessed through:  World Register of Marine Species at http://www.marinespecies.org/aphia.php?p=taxdetails&id=101 on 2016-10-11',NULL,false,false,false,'E');

Use file: GET WORMS OUTPUT INTO FORMAT FOR ONEBENTHIC.csv to get worms output into format for inserting into table 'taxa'

# To insert data into table 'cluster' (output from cluster ID tool)
\COPY derived_data.cluster FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\clusterinput2.csv' DELIMITER ','NULL AS '[null]' CSV HEADER;

# To insert data into table 'sedvarsample' (OneBenthic on azure)
\COPY sediment_data.sedvarsample FROM 'C:\Users\kmc00\OneDrive - CEFAS\OneBenthicDB\RSMP PSA ONLY STATIONS\A2018psalong.csv' DELIMITER ',' CSV HEADER;

# To insert missing record into table worrmstaxa
INSERT INTO faunal_data.worrmstaxa (id,aphia_aphiaid,taxonname) VALUES (13080, 124672,'Thyone inermis')

# to update incorrect lat and longs
http://www.postgresqltutorial.com/postgresql-update/
UPDATE sample
SET samplelat = '50.489304',
samplelong = '0.875055'
WHERE
samplecode = '003.1.11155';
correct data in 'east channel 2013 correct positions.csv'

# to add a new column to a table
 ALTER TABLE associations.station ADD COLUMN active boolean NOT NULL DEFAULT true;

# to update a column value in 
 UPDATE associations.station SET active = FALSE WHERE stationcode = 'EC_0470';

## QUERYING REVISITED
Syntax:
SELECT tablename.colname, table.colname
FROM table1 AS t1
? JOIN table2 AS t2
ON t1.colID = T2.colID
WHERE condition(s)
ORDER

JOIN types:
INNER - only returns matching rows
LEFT - every row from left table
RIGHT - every row from right table
FULL - every row from both tables

Use alias to avoid repeating table name with AS

# Query to extract sediment data for all RSMP stations
SELECT samst.station_stationcode, sam.samplecode, sed.sedvar_sievesize, sed.percentage, sam.year
FROM samples.sample AS sam
INNER JOIN sediment_data.sedvarsample AS sed
ON sam.samplecode = sed.sample_samplecode
INNER JOIN associations.samplestation AS samst
ON sam.samplecode = samst.sample_samplecode
ORDER BY station_stationcode, samplecode, sedvar_sievesize desc;

# Get faunal data by survey
SELECT su.surveyname,
s.samplecode,
s.samplelat,
s.samplelong,
g.gearname,
s.date,
s.macrosieve,
ts.worrmstaxa_taxonname,
ts.taxaqual_qualifier,
w.validname,
w.rank,
tq.qualifiername,
a.worrms_validaphiaid,
ts.abund,
su.datapubliclyavailable,
s.samplecode2,
o.ownername,
s.waterdepth,
s.grabsamplesize,
g.geartype_geartype
                  
FROM 
associations.survey as su
INNER JOIN associations.surveysample as ss ON ss.survey_surveyname = su.surveyname 
INNER JOIN samples.sample as s ON ss.sample_samplecode = s.samplecode
INNER JOIN gear.gear as g ON s.gear_gearcode = g.gearcode
INNER JOIN faunal_data.taxasample as ts ON s.samplecode= ts.sample_samplecode 
LEFT JOIN faunal_data.taxaqual as tq ON ts.taxaqual_qualifier = tq.qualifier 
INNER JOIN faunal_data.worrmstaxa as wt ON wt.taxonname = ts.worrmstaxa_taxonname 
INNER JOIN faunal_data.aphia as a ON wt.aphia_aphiaid = a.aphiaid 
INNER JOIN faunal_data.worrms as w ON w.validaphiaid = a.worrms_validaphiaid
INNER JOIN associations.sampleowner as so ON so.sample_samplecode = s.samplecode
INNER JOIN associations.owner as o ON so.owner_ownername = o.ownername
WHERE su.surveyname = 'Area 457 Regional Seabed Monitoring Plan 2019' 
ORDER by s.samplecode;

# To find column names for table?
SELECT column_name
FROM information_schema.columns 
WHERE table_schema = 'faunal_data' 
AND table_name = 'worrms'

# To input new records into table faunal_data.worrms 
use R script (C:\Users\kmc00\OneDrive - CEFAS\R_PROJECTS\OneBenthicPrepareTablesForInput\get new taxon record for input to tbl faunal_data_worrms.R)

SQL code:
INSERT INTO faunal_data.worrms
(aphiaid,  url,  scientificname,  authority,  status,  unacceptreason,  taxonrankid,  "rank",  validaphiaid,  validname,  validauthority,  parentnameusageid,  kingdom,  phylum,  "class",  "order",  "family",  genus,  citation,  lsid,  ismarine,  isbrackish,  isfreshwater,  isterrestrial,  isextinct,  matchtype,  modified,  include,  nonnative,  conservationimportance,  countable,  commonname,  description,  habitat,  nativerange,  firstdiscovered,  pathway,  imagepath)
VALUES (124672,'http://www.marinespecies.org/aphia.php?p=taxdetails&id=124672', 'Thyone inermis', 'Heller, 1868', 'accepted',NULL, 220, 'Species',124672, 'Thyone inermis','Heller, 1868', '146116','Animalia', 'Echinodermata', 'Holothuroidea', 'Dendrochirotida', 'Phyllophoridae', 'Thyone', 'WoRMS (2020). Thyone inermis Heller, 1868. Accessed at: http://www.marinespecies.org/aphia.php?p=taxdetails&id=124672 on 2020-02-06', 'urn:lsid:marinespecies.org:taxname:124672',TRUE,FALSE,FALSE,FALSE,NULL,'exact','2015-12-18T11:12:15Z',TRUE,FALSE,FALSE,TRUE,NULL,NULL,NULL,NULL,NULL,NULL,NULL)

## DB Backup
Using the command line in default directory: C:\Users\kmc00>

pg_dump -U editors_one_benthic@azsclnxgis-ext01 -h azsclnxgis-ext01.postgres.database.azure.com -p 5432 -O one_benthic > one_benthicbackup13022020.sql

(-O: no owner)
## Create new DB based on OneBenthicLive
Create a new DB in PgAdmin4 with same name as pg_dump backup file

e.g. 'one_benthicbackup13022020'

Using the command line in default directory: C:\Users\kmc00>

psql -U postgres -h localhost -p 5433 one_benthicbackup13022020 < one_benthicbackup13022020.sql

PW:postgres1234

# To delete duplicated rows in a table
## First find number of rows: 1236735
SELECT DISTINCT * FROM faunal_data.taxasample;

## Second, find number of distinct rows: 1164251
SELECT DISTINCT ON (sample_samplecode,worrmstaxa_taxonname,taxaqual_qualifier,abund,biomass)* FROM faunal_data.taxasample

## Third find duplicated rows (1236735-1164251=72484)
Select sample_samplecode,worrmstaxa_taxonname,taxaqual_qualifier,abund,biomass,count(*)
from faunal_data.taxasample
group by sample_samplecode,worrmstaxa_taxonname,taxaqual_qualifier,abund,biomass
having count(*)>1

## Fourth, delete duplcate rows
  with a as ( 
  select row_number () over( partition by sample_samplecode, worrmstaxa_taxonname,taxaqual_qualifier, abund, biomass
							order by sample_samplecode) dup ,count(id) over ( ) total,  id  
from faunal_data.taxasample ) 
delete from faunal_data.taxasample where id in ( select distinct id  from a where  dup > 1 )

