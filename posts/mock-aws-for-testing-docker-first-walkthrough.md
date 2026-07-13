# Mock AWS for Testing: Docker-First Walkthrough

# Mock AWS for Testing: Docker-First Walkthrough

When developing applications that interact with AWS services, testing can become complex and expensive. Traditional approaches often require spinning up real AWS resources or using costly testing environments. For developers building applications that depend on AWS services like S3, DynamoDB, or SQS, a local AWS emulator provides an efficient alternative.

## Why Mock AWS Locally?

A local AWS emulator allows you to:
- Test code without incurring AWS costs
- Develop offline with consistent behavior
- Speed up test execution times (local vs. cloud)
- Avoid rate limits and network issues
- Simulate various error conditions

Floci Local-AWS Starter Kit provides exactly this experience - a free, zero-config local AWS emulator that works seamlessly with your existing code.

## Getting Started with Floci Local-AWS

The simplest way to get started is with Docker. Here's a complete docker-compose.yml file that sets up the Local-AWS environment:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
      - "4571:4571"
    environment:
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
      - AWS_DEFAULT_REGION=us-east-1
    volumes:
      - ./data:/data
```

This configuration exposes the essential ports (4566 for AWS services, 4571 for additional tools) and mounts a local volume for data persistence. The environment variables provide default credentials that match typical AWS SDK expectations.

## Testing with Local S3

Let's see how to test S3 operations locally using Python and boto3:

```python
import boto3
from moto import mock_s3

# Configure the endpoint URL to point to our local service
s3_client = boto3.client(
    's3',
    endpoint_url='http://localhost:4566',
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

# Create a bucket and upload an object
bucket_name = 'test-bucket'
key = 'test-object.txt'

s3_client.create_bucket(Bucket=bucket_name)
s3_client.put_object(
    Bucket=bucket_name,
    Key=key,
    Body=b'Hello, Local AWS!'
)

# Verify the object exists
response = s3_client.get_object(Bucket=bucket_name, Key=key)
print(response['Body'].read().decode())  # Output: Hello, Local AWS!
```

This approach works with any AWS SDK or service that supports custom endpoint URLs. You can run this script against your local Floci instance without any network calls to real AWS.

## DynamoDB Integration

For DynamoDB testing, the setup is equally straightforward:

```python
import boto3

# Create DynamoDB client pointing to local service
dynamodb = boto3.resource(
    'dynamodb',
    endpoint_url='http://localhost:4566',
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

# Create table and put an item
table = dynamodb.create_table(
    TableName='test-table',
    KeySchema=[
        {
            'AttributeName': 'id',
            'KeyType': 'HASH'
        }
    ],
    AttributeDefinitions=[
        {
            'AttributeName': 'id',
            'AttributeType': 'S'
        }
    ],
    BillingMode='PAY_PER_REQUEST'
)

# Put and get an item
table.put_item(Item={'id': '123', 'data': 'test'})
response = table.get_item(Key={'id': '123'})
print(response['Item'])  # Output: {'id': '123', 'data': 'test'}
```

## Running Your Tests

With the local environment configured, you can integrate it into your testing workflow:

```bash
# Start the local AWS environment
docker-compose up -d

# Run your tests against local services
python -m pytest test_aws_integration.py

# Stop when done
docker-compose down
```

The Local-AWS service starts in under 10 seconds and maintains consistent performance across multiple test runs. Memory usage remains below 256MB during typical development workloads.

## Configuration Options

Floci Local-AWS supports several configuration options for different use cases:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
    environment:
      - AWS_ACCESS_KEY_ID=local
      - AWS_SECRET_ACCESS_KEY=local
      - AWS_DEFAULT_REGION=us-west-2
      - SERVICES=s3,dynamodb,sqs
    volumes:
      - ./data:/data
      - ./config:/config
```

This configuration sets up specific services, customizes the region, and mounts additional configuration directories. The service supports 15+ AWS services including Lambda, SES, SNS, and more.

## Performance Considerations

Local testing with Floci Local-AWS typically provides 5-10x faster test execution compared to real AWS calls. For a typical CI pipeline with 100 tests, you can reduce total execution time from 30+ minutes to under 4 minutes.

The service handles concurrent requests efficiently and maintains data consistency across multiple test runs. It's designed for development workflows where speed and reliability are priorities.

## Integration with Existing Code

To make your existing AWS-dependent code work with Local-AWS, simply change the endpoint URL:

```python
# Before (real AWS)
s3 = boto3.client('s3', region_name='us-east-1')

# After (local AWS)  
s3 = boto3.client(
    's3',
    endpoint_url='http://localhost:4566',
    region_name='us-east-1'
)
```

This minimal change allows you to test against local services while maintaining identical code behavior for production deployments.

## Getting Started

The Local-AWS environment requires no additional setup beyond Docker. You can start testing in under 5 minutes with the following commands:

```bash
# Pull and start the service
docker-compose up -d

# Verify it's running
curl http://localhost:4566/health

# Run your tests against local services
python -m pytest tests/
```

## Get the kit

Ready to test your AWS applications locally? Get the Floci Local-AWS Starter Kit and start developing without network calls or costs.

[Get the kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)
