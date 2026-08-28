**Overall Pipeline**

<img width="1077" height="499" alt="image" src="https://github.com/user-attachments/assets/5b0967d1-998c-43a4-9eb1-b1d0c0d1b167" />


**Dataflow Gen2**

<img width="1835" height="851" alt="image" src="https://github.com/user-attachments/assets/a5c18ed5-6926-47fc-95e1-bb21b912382a" />


**Create the dbo.nyctaxi_yellow table**
This is the initial empty table so we can load the data from the Dataflow/Stored Procedure acivities

    CREATE TABLE dbo.nyctaxi_yellow
    (
    	vendor varchar(50),
    	tpep_pickup_datetime date,
    	tpep_dropoff_datetime date,
    	pu_borough varchar(100),
    	pu_zone varchar(100),
    	do_borough varchar(100),
    	do_zone varchar(100),
    	payment_method varchar(50),
    	passenger_count int,
    	trip_distance FLOAT,
    	total_amount FLOAT
    );


**SP Processing Presentation**

    CREATE PROCEDURE dbo.process_presentation
    AS
    INSERT INTO dbo.nyctaxi_yellow
        SELECT
        CASE 
            WHEN nty.VendorID = 1 THEN 'Creative Mobile Technologies'
            WHEN nty.VendorID = 2 THEN 'VeriFone'
            else 'Unknown'
        end as vendor,
        format(nty.tpep_pickup_datetime,'yyyy-MM-dd') as tpep_pickup_datetime,
        format(nty.tpep_dropoff_datetime,'yyyy-MM-dd') as tpep_dropoff_datetime,
        lu1.Borough as pu_borough,
        lu1.Zone as pu_zone,
        lu2.Borough as pu_borough,
        lu2.Zone as pu_zone,
        CASE 
            WHEN nty.payment_type = 1 THEN 'Credit Card'
            WHEN nty.payment_type = 2 THEN 'Cash'
            WHEN nty.payment_type = 3 THEN 'No Charge'
            WHEN nty.payment_type = 4 THEN 'Dispute'
            WHEN nty.payment_type = 5 THEN 'Unknown'
            WHEN nty.payment_type = 6 THEN 'Voided Trip'
            else 'Unknown'
        end as payment_method,
        nty.passenger_count as passenger_count,
        nty.trip_distance as trip_distance,
        nty.total_amount as total_amount
        from stg.nyc_taxi_yellow nty
        left join stg.taxi_zone_lookup lu1
        on nty.PULocationID = lu1.LocationID
        left join stg.taxi_zone_lookup lu2
        on nty.DOLocationID = lu2.LocationID;


**SP Loading Presentation Metadata**

    CREATE PROCEDURE metadata.insert_presentation_metadata
        @pipeline_run_id VARCHAR(255),
        @table_name VARCHAR(255),
        @processed_date DATETIME2
    AS
        INSERT INTO metadata.processing_log (pipeline_run_id, table_processed, rows_processed, latest_processed_pickup, processed_datetime)
        SELECT
            @pipeline_run_id AS pipeline_id,
            @table_name AS table_processed,
            COUNT(*) AS rows_processed,
            MAX(tpep_pickup_datetime) AS latest_processed_pickup,
            @processed_date AS processed_datetime
        FROM dbo.nyctaxi_yellow;

<img width="904" height="265" alt="image" src="https://github.com/user-attachments/assets/b3d5f0a6-992d-4483-999c-653ca2eeb5f9" />



  
