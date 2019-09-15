# Readme file for Google Cloud Billing Catalog app

## Tables created

The program creates 3 different tables to store information for the Google Cloud Billing Catalog. Following is information about the tables:

### 1. Services table

First tables holds the basic information for each service. The information includes the *service id* (which uniquely identifies a  google cloud service) and the *service name*. 

| service_id | service_name |
| ---------- | ------------ |
| *String*   | *String*     |

### 2. SKUs table

Second table holds information about each SKU of a google cloud service. The information stored in this table is a *sku id* (which uniquely identifies a google cloud sku), *service id* for which this sku belongs too, *sku description* (which holds a little bit of information regarding what is included in this sku, for example the specification of the compute engine etc.), and the *effective time* (a time since this sku  and its services are valid). 

| sku_id   | service_id | description | effective_time |
| -------- | ---------- | ----------- | -------------- |
| *String* | *String*   | *String*    | *String*       |

### 3. Rates table

Third and last table holds the actual information about rates of each sku. This table uses a composite key, which is composed of *sku id* and the *start_usage_amount* which, when combined, help uniquely identify a tier rate for a service. The table also includes *units* (main unit of a currency) and the *nanos* (which is equivalent to 1/1000000000 of the main unit of currency). It also includes the *currency code* and the *formatted price* which combines the *units* and *nanos* to display the final price of a service unit.

| sku_id   | start_usage_amount | units    | nanos    | currency_code | formatted_price | updated_on |
| -------- | ------------------ | -------- | -------- | ------------- | --------------- | ---------- |
| *String* | *Number*           | *Number* | *Number* | *String*      | *Decimal*       | *Date*     |

## Charting price for a service

To chart price over time for a service, following steps will be taken:

1. Get the service id for the service from the services table
2. Get all the skus for the service from the skus table
3. For each sku, get all the rates from the rates table, each sku will have multiple tiered rates, depending on the start usage amount. Search can be further narrowed down by specifying the start usage amount too. The date at which the rate was applicable can be deduced from the updated_on variable.

## Configurations needed for running

Following are the pre-requisites for running the app:

- Serverless framework (can be installed though npm)
- AWS Credentials configured on the system (easily done through aswscli: *aws config*)
- A google cloud API key

In addition to that, you can configure following settings in serverless.yml file before deploying app:

- ServicesTableName: Name of the table to be created on DynamoDB for services' information
- SKUsTableName: Name of the table to be created on DynamoDB for skus' information
- RatesTableName: Name of the table to be created on DynamoDB for rates' information
- S3BucketName: Name of the bucket to be created to store the raw data for service prices in JSON format
- GoogleCloudAPIKey: API Key for the google account, required to call the google billing catalog api

To connect to the serverless.com dashboard, either configure the serverless credentials in the cli tool, or uncomment and set the app and org values in the serverless.yml file.

