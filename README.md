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

**Code explanation**:

<img width="800" height = "400" alt="image" src="https://github.com/balajide19/REAL-TIME-PRODUCT-SALES-PROJECTION/assets/146630003/f4375d6a-1546-4b43-b7b5-dcb7b642d8ca">






