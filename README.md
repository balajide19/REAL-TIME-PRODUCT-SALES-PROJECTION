# REAL-TIME-PRODUCT-SALES-PROJECTION

<img width="600" height="400" alt="image_2023-10-09_10-45-18" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/e651cc40-5dd8-4247-b2cf-f4271db850f8">

# PROBLEM:

1) To build a real time pipeline where CDC (Change data capture) events from dynamoDB are captured using DynamoStream and it should be published to consumers (Kinesis stream).
2) The records from kinesis stream should be batched and inserted in S3.
3) Then stored records in S3 must be crawled and used for analysis using Athena.

# TECH STACKS USED:

1) AWS DynamoDb and DynamoStream
2) Eventbridge Pipe
3) Kinesis stream and Firehose
4) AWS S3 bucket
5) Athena

# BUILDING DATA PIPELINE:

DATASET:

For achieving streamless data I generated mock data using python "random" module.

below script generates random order data and inserts it into an AWS DynamoDB table using the boto3 library. The script runs indefinitely, inserting data every 3 seconds, until manually interrupted.

[mock_data_generator_for_dynamodb.py](https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/blob/9fecd2d6e1fe585c66b85fb13dd4dad7dae342c3/mock_data_generator_for_dynamodb.py)

**CODE EXPLANATION**:

**IMPORT STATEMENTS:**

<img width="450" height="200" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/873d5195-8fee-4f43-8456-ecd79b55c795">

'boto3' --> For interacting with AWS services using our Python scripts.
'random' --> This python module helps us to generate random data.
'time' --> To work with time-related operations in our Python programs as we need to use this for defining sleep time.
'decimal' --> For handling decimal values with precision.

**AWS Session and DynamoDB Table Initialization:**

<img width="650" height="250" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/120e15be-7c77-4e9a-8db8-00832105af8f">

1) Using boto3 we're creating a session with a specified AWS profile and region and a DynamoDB resource is initialized using the session.
2) A specific DynamoDB table named 'OrderTable' is assigned to the table variable.

**GENERATING RANDOM DATA AND INSERTING IT IN DYNAMODB:**

<img width="648" height="300" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/4f5d002e-178d-460a-aa28-bac825d2a04d">

1) Using generate_order_data function we will generate random order data, including an order ID, product name, quantity, and price and the data is returned as a dictionary.
2) Once random data is generated we will use 'insert_into_dynamodb' function for inserting data into the DynamoDB table. It uses the put_item method to add the provided data to the table.
3) If there's an error during insertion, it will be handled by our defined try exception block.

**MAIN EXECUTION:**

<img width="608" height="200" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/ac6222a6-aff8-4502-96ea-51c0af866837">

1) The script enters the main execution block.
2) Inside a while loop, it continuously generates random order data, inserts it into DynamoDB, and then sleeps for 3 seconds.
3) This loop continues indefinitely until it's manually interrupted.
4) If its manually interrupted, it will prints a message indicating that the script has been stopped.

# DEPLOYMENT:

Once mock_data is generated records will be inserted into dynamoDB and our dynamoDB stream is "Turned ON" for capturing CDC events.

<img width="700" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/15c80ab2-d55c-43f1-90cd-56906a80c6b8">


**INTEGRATION BETWEEN DYNAMODB AND KINESIS USING EVENTBRIDGE PIPE:**

<img width="800" height="400" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/ce4e3a16-a42d-4ebd-9d83-3b029ba2b03e">

1) Using Eventbridge Pipe we are integrating Dynamodb and Kinesis stream.
2) Here the Source is "DynamoDb" , Target is "Kinesis", StartingPosition: "LATEST" as we will receive only the latest records and Batch size is "1"
3) Here I didn't want any filter, enrichment so avoiding those steps in EB pipe.

**BATCHING DATA USING FIREHOSE:**

<img width="933" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/946a9481-2549-44a9-94b5-877a8728efd3">

<img width="936" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/55b09d9a-c281-40d7-8d88-83b558c10934">


1) Once the data is published in dynamoDB, it will be consumed by our exisiting "kinesis-orderstream" stream  using EB pipe.
2) Since, its a stream data if we enter each records into "S3" more partition will be created resulting in more space usage.
3) To avoid it, we will batch our data using "Kinesis Firehose".

TRANSFORMATION OF DATA USING LAMBDA FUNCTION:

Before loading the data from Firehose to S3 we are using "transformlambda" lambda function for decode the encoded data in the record and then transform it into JSON
format.

Below is the Lambda code:

[transformation_layer_with_lambda.py] (transformation_layer_with_lambda.py)

**CODE EXPLANATION:**

**Importing Libraries:**

1) Using base64 module for encoding and decoding.
2) Importing The json module is for working with JSON data.

**LAMBDA HANDLER FUNCTION:**

This function is the entry point for the AWS Lambda function. It takes two arguments: event and context.

<img width="700" height="500" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/cc202332-d4f4-4b53-be28-16f514977e04">






   










