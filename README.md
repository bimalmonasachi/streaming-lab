# streaming-lab
Pyspark Streaming Lab 
## Problem Definition
<h2><img src="https://files.training.databricks.com/images/105/logo_spark_tiny.png"></h2>
Please write a program in any language of your choice which supports spark processing. The program
must implement a logic to stream credit card transactions from a CSV and calculate the count of
transactions over last 3 minutes with a sliding window of 2 minute. 

Please find additional instructions below:
* Must use SPARK streaming
* The CSV will need to have at least 10,000 transactions
* Please use MOCKNEAT (https://github.com/nomemory/mockneat) to generate the credit card transaction

## Solution Approach:
* Used Structured Streaming technique with sliding window 
* Defined `3 minute window` with `sliding interval of 2 minutes`
* Create the streaming data frame to read one file from data files stored in AWS S3 for every trigger
* Group the data by window and count the transactions for each group
* Created the write stream and for our lab(debugging) purpose, used the `sink` as `console` so we can see the output on the console
* Gracefully stop the stream after it has processed all the files
* Have created 100 data files with credit card transactions, each file has 100 records. `Helps to simulate streaming by reading each file every 2 minute trigger`.

### Read Me - With Assumptions
* Used (generatedata.com) site to generate the credit transactions
* Files are split into 100 transactions to emulate slow streaming by reading 1 file during each trigger.
* The Development environment used - `Databricks Community Edition Spark Cluster, S3 for storage of data file, Jupyter Notebook with pyspark`.
* The data files were uploaded to AWS s3 storage and used during running of the code. PLEASE MAKE APPROPRIATE changes to `dataPath` depending on where you clone and store the data files.
* The data file consists of the following columns:

| Field              | Description                  |
|-----------------   |------------------------------|
| `Transaction-date` | Transaction column           |
| `Name`             | Customer Name                |
| `Credit Card`      | Used MasterCard              |
| `Amount`           | Transaction Amount           |

#### Note - The field delimiter is pipe (`|`) 


### Step1: Sample one file by Reading data stored in S3 in a data frame

Take a look at the first few lines of the dataset in one file.
##### Note that the `Transaction-date` when we use `inferSchema` get loaded as `String data type`

### Step2: Define the schema and load the file 
#### confirm that the `transaction-date` is of `timestamp type`


### Step3 - Now we will need to do the following tasks:
* Define the data stream that reads `csv` files dumped to the directory `dataPath`
* Control the size of each partition by forcing Spark to processes only 1 file per trigger.

Other notes:
* The source data has been defined as `dataPath` to read all the files in the directory
* The schema has already be defined as `dataSchema`

### Step 4:
* Define the sliding window of `3 minutes` with slide inteval of `2 minutes`
* Group the data by window and count the data for each group
* Using 6 minute watermark

#### Note - Handling late arrival and also avoiding OOM(Out of Memory or crashing of the cluster) Using `Watermarking` technique of streaming 
#### Note - Watermark may be neglected in my code since i have used `output mode as complete` but have added to show the best practice

### Step 5: Write Stream to approriate sink.
#### Note - Have used `console` for debugging and lab purpose but in real production pipeline, we use file check-point with write-ahead logs for fault-tolerance 

* Write the stream to  `console` 
* For this lab, we will use `complete` output mode
* Configure a `2 minute` trigger aligning with our `2 minute` sliding window
* Start the query
* Have used at the end  **`awaitTermination()`** followed by **`query.stop()`** for graceful exit.


