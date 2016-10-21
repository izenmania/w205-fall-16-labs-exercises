MIDS W205

Lab#              5        LabTitle          WorkingwithRelationalDatabases

RelatedModule(s)  5        Goal              GetyouintroducedtoaRDBMS(PostgreSQL)
LastUpdated       2/14/16  Expectedduration  40minutes



Introduction,Resources

Whileourinitialinvestigations have dealt with Hive and SparkSQL, often as a Data Scientist, you
will encounter relational databases like PostgreSQL. Inthislab,wewilllearnaboutthefollowing:

     1. HowtocreateadatabaseinPostgreSQL
     2. HowtoloaddataintoPostgreSQL
     3. HowtorunqueriesonPostgreSQL
     4. HowqueriesaretransformedintoplansforDAGsinPostgreSQL

Resource                                          What
                                                  PostgreSQLDocumentation
http://www.postgresql.org/docs/9.5/static/index.  TheSQLLanguage
html
http://www.postgresql.org/docs/9.5/static/sql.ht
ml




Step-1.Setuptheenvironment

We need to setup an EC2 instance and make sure that PostgreSQL is up and running. Do the
following:

1. LaunchaninstanceofUCBW205Spring2016

a. Attach your EBS volume from Lab 2. Note that PostgreSQL should be installed
    afteryoufinishstep3.4ofLab2

b. CheckwhetherPostgreSQLisupandrunning:

ps auxwww | grep postgres
    c. Ifnot,changeyourcurrentpathto/data:

                i. cd /data
                  ii. StartPostgres:/data/start_postgres.sh
2. GettingtheData:         
    

                                                                                   1
             a. We need some data in order to create a database, schema and, ultimately,
                  query.Thedatawe'llconsiderisatoydatasetDVDrental.

             b. Navigatetothe/datadirectoryonyourAWSinstanceanddownloadthePagila
                  dataasfollows:

                  wget -O pagila.zip
                  http://pgfoundry.org/frs/download.php/1719/pagila-
                  0.10.1.zip

             a. Unzipthedata

                  unzip pagila.zip

    3. ConnectingtothePostgreSQLinstance,creatingadatabase,andimportingthedata:

             a. Logintopostgresasthepostgresuser:

                  psql ­U postgres

             b. Createthedatabase:


                  create database dvdrental;


             c. Connecttothedatabaseusing\c


                  \c dvdrental


             d. Loadthedatausingthe\icommand.\iruns.sqlscriptsinPostgres.


                  \i pagila-0.10.1/pagila-schema.sql
                  \i pagila-0.10.1/pagila-insert-data.sql
                  \i pagila-0.10.1/pagila-data.sql


Atthispointthedataisloaded.Examinethedatabaseschemausingthe\dtcommand.
Examinetheschemaofatableusingthe\d <table name>command

                                                                                                                                 2
Question1:Whatistheoutputof\dt?

Question2:Whatistheschemaforthecustomertable?



Step2.RunningQueriesandUnderstandingEXPLAINplans

Wewanttounderstandnotonlywhatquerieswecanissueagainstdata,butalsohowthat
querymapstoanexecutionplan.Foreachofthefollowingsections,runthequeriesprovided,
andgeneratetheirexplainplansusing:EXPLAIN <sql query here>


ProjectionandSelection
Runthefollowingsimplequeries,thengeneratetheirexplainplans.

Projection:

         SELECT customer_id, first_name, last_name FROM customer;

ProjectionandSelection#1:


         SELECT customer_id,
          amount,
          payment_date

         FROM payment
         WHERE amount <= 1 OR amount >= 8;


ProjectionandSelection#2:

         SELECT

                  customer_id,
                  payment_id,
                  amount
         FROM
                  payment
         WHERE
                  amount BETWEEN 5
         AND 9;

                                                                                                                                 3
Question3:Whatsimilaritiesdoyouseeintheexplainplainsforthese3queries?

MergingData:JOINsandUNIONs:

Runthefollowingstatements:

Union2tables:



         SELECT u.customer_id, sum(u.amount) from (
            SELECT *
            FROM
                  payment_p2007_01
            UNION
            SELECT *
            FROM
                  payment_p2007_02

         )u
         WHERE u.payment_date <= '2007-02-01 00:00:00'::timestamp
without time zone
         GROUP BY u.customer_id
         ;

PartitionaTable:

         SELECT customer_id, sum(amount) from
         payment
         WHERE payment_date <= '2007-02-01 00:00:00'::timestamp
without time zone
         GROUP BY customer_id
         ;


Question4:WhatisthedifferencebetweentheplansforthePartitionedtableandtheunion
query?Whydoyouthinkthisdifferenceexists?

Join2tables:


         SELECT
                  customer.customer_id,
                  first_name,

                                                                                                                                 4
                  last_name,
                  email,
                  amount,
                  payment_date
         FROM
                  customer
         INNER JOIN payment ON payment.customer_id =
customer.customer_id;

Question5:Whatjoinalgorithmisusedfortheinnerjoin?

Finally,disconnectfrompostgres,using\q

Submissions

SubmityouranswerstothequestionsthroughISVCasatextfile,docxfile,orPDF.





                                                                                                                                 5
