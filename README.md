Welcome to the Brickhouse
=========================

Followed link
http://stackoverflow.com/questions/24607685/loading-xml-data-into-hive-table-org-apache-hadoop-hive-ql-metadata-hiveexcepti






---------------------------------------------------------------------------


ADD jar /home/daas/brickhouse-0.7.0-SNAPSHOT.jar;  --Add brickhouse jar 

CREATE TEMPORARY FUNCTION array_index AS 'brickhouse.udf.collect.ArrayIndexUDF';
CREATE TEMPORARY FUNCTION numeric_range AS 'brickhouse.udf.collect.NumericRange';

DROP table xmltable;

CREATE EXTERNAL table xmltable  (xmldata string)
LOCATION '/daastest/hivexml';
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n'
LOCATION '/daastest/hivexml';


select xpath(xmldata, '/sessionList/session/@appName') from xmltable;


drop view MyxmlView;

CREATE VIEW MyxmlView(appName) AS
SELECT
xpath(xmldata, '/sessionList/session/@appName')
 FROM xmltable;
 
 select * from MyxmlView;
 
 
 SELECT 
    array_index( appName, n ) as source
 from MyxmlView
lateral view numeric_range( size( appName )) MyxmlView as n;












-------------------------------------------------------------------------------





[![Build Status](https://travis-ci.org/klout/brickhouse.svg?branch=master)](https://travis-ci.org/klout/brickhouse)

   Brickhouse is a collection of UDF's for Hive to improve developer 
   productivity, and the scalability and robustness of Hive queries.
   

  Brickhouse covers a wide range of functionality, grouped in the 
     following packages.

 * _collect_ - An implementaion of "collect"  and various utilities
     for dealing with maps and arrays.
   
 * _json_ - Translate between Hive structures and JSON strings

 * _sketch_ - An implementation of KMV sketch sets, for reach 
     estimation of large datasets.

 * _bloom_ - UDF wrappers around the Hadoop BloomFilter implementation.

 * _sanity_ - Tools for implementing sanity checks and managing Hive
	  in a production environment.
   
 * _hbase_ - Experimental UDFs for an alternative way to integrate
	  Hive with HBase.
     
Requirements:
--------------
  Brickhouse require Hive 0.9.0 or later;
  Maven 2.0 and a Java JDK is required to build.

Getting Started
---------------
 1. Clone ( or fork ) the repo from  https://github.com/klout/brickhouse 
 2. Run "mvn package" from the command line.
 3. Add the jar "target/brickhouse-\<version number\>.jar" to your HIVE_AUX_JARS_FILE_PATH,
    or add it to the distributed cache from the Hive CLI 
    with the "add jar" command
 4. Source the UDF declarations defined in src/main/resource/brickhouse.hql

See the wiki on Github at https://github.com/klout/brickhouse/wiki for more 
  information.

Also, see discussions on the Brickhouse Confessions blog on Wordpress 
 
 http://brickhouseconfessions.wordpress.com/
 

[![DOI](https://zenodo.org/badge/4948/klout/brickhouse.png)](http://dx.doi.org/10.5281/zenodo.10751)





----------------




--Load xml data to table
DROP table xmltable;
Create TABLE xmltable(xmldata string) STORED AS TEXTFILE;
LOAD DATA lOCAL INPATH '/home/vijay/data-input.xml' OVERWRITE INTO TABLE xmltable;

-- check contents
SELECT * from xmltable;

-- create view
Drop view  MyxmlView;
CREATE VIEW MyxmlView(id, genre, price) AS
SELECT
 xpath(xmldata, 'catalog/book/id/text()'),
 xpath(xmldata, 'catalog/book/genre/text()'),
 xpath(xmldata, 'catalog/book/price/text()')
FROM xmltable;

-- check view
SELECT id, genre,price FROM MyxmlView;


ADD jar /home/vijay/brickhouse-0.7.0-SNAPSHOT.jar;  --Add brickhouse jar 

CREATE TEMPORARY FUNCTION array_index AS 'brickhouse.udf.collect.ArrayIndexUDF';
CREATE TEMPORARY FUNCTION numeric_range AS 'brickhouse.udf.collect.NumericRange';

SELECT 
   array_index( id, n ) as my_id,
   array_index( genre, n ) as my_genre,
   array_index( price, n ) as my_price
from MyxmlView
lateral view numeric_range( size( id )) MyxmlView as n;




