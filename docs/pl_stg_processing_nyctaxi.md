**Overall Pipeline**

<img width="1843" height="495" alt="image" src="https://github.com/user-attachments/assets/c9854283-a052-462d-8f01-04bdef994d0e" />


**Latest Processed Data**

	select top 1 
	latest_processed_pickup 
	from metadata.processing_log 
	where table_processed = 'staging_nyctaxi_yellow'
	order by latest_processed_pickup desc;


**v_date**

	@formatDateTime(addToTime(activity('Latest Processed Date').output.resultSets[0].rows[0].latest_processed_pickup, 1, 'Month'), 'yyyy-MM')


**Copy to Staging**
Pre Copy Script

<img width="725" height="266" alt="image" src="https://github.com/user-attachments/assets/e4ab373f-a790-4774-9760-57b0377bd2fc" />


**v_end_date**

	@addToTime(concat(variables('v_date'), '-01'),1,'Month')


**SP Removing Outlier Dates**
Create the Stored Procedure stg.data_cleaning_stg in the Data Warehouse using the code below.

	create procedure stg.data_cleaning_stg
	@end_date datetime2,
	@start_date datetime2
	as
	delete from stg.nyctaxi_yellow where tpep_pickup_datetime < @start_date or tpep_pickup_datetime > @end_date;

<img width="858" height="243" alt="image" src="https://github.com/user-attachments/assets/c1363330-64c3-4489-86c9-ffe997c945c6" />


**SP Loading Staging Metadata**
Code to create the metadata.processing_log table.

	create schema metadata;
	
	create table metadata.processing_log
	(
		pipeline_run_id varchar(255), 
		table_processed varchar(255), 
		rows_processed INT, 
		latest_processed_pickup datetime2(6),
		processed_datetime datetime2(6)
	);

Created the Stored Procedure metadata.insert_staging_metadata in the Data Warehouse using the code below.

	CREATE PROCEDURE metadata.insert_staging_metadata
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
	    FROM stg.nyctaxi_yellow;

<img width="884" height="284" alt="image" src="https://github.com/user-attachments/assets/6e6d6ce8-dd37-4f35-865d-dcc2b2f2dcd2" />

