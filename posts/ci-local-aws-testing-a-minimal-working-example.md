# Ci Local Aws Testing: A Minimal Working Example

# Ci Local Aws Testing: A Minimal Working Example

When developing AWS applications locally, you need a reliable emulator that behaves exactly like the real AWS services. Floci Local-AWS Starter Kit provides exactly this — a zero-configuration local AWS environment that mirrors real AWS behavior.

## Why Local AWS Matters for CI/CD

In continuous integration pipelines, we want to test our AWS-dependent code without:
- Incurring costs
- Waiting for real AWS service calls
- Dealing with network latency
- Managing credentials in CI environments

The Local-AWS emulator allows you to run tests against services like S3, DynamoDB, Lambda, and SQS locally, then seamlessly switch to real AWS when needed.

## Quick Setup Example

Here's a minimal working example using Docker Compose:

```yaml
# docker-compose.yml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"  # Local AWS endpoint
    environment:
      - AWS_DEFAULT_REGION=us-east-1
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
```

Run with:
```bash
docker-compose up -d
```

This starts a local AWS-compatible service on port 4566, which is the default for LocalStack and similar tools.

## Real AWS Client Configuration

Your code can point to this local service using standard AWS SDK configurations:

```python
# test_s3.py
import boto3
from moto import mock_s3

# Configure AWS client to use local endpoint
s3_client = boto3.client(
    's3',
    endpoint_url='http://localhost:4566',
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

# Create bucket and upload object
bucket_name = 'test-bucket'
s3_client.create_bucket(Bucket=bucket_name)
s3_client.put_object(
    Bucket=bucket_name,
    Key='test-key',
    Body=b'test content'
)

# Verify the object exists
response = s3_client.get_object(Bucket=bucket_name, Key='test-key')
print(response['Body'].read().decode())  # Output: test content
```

## Service Coverage and Performance

The Local-AWS Starter Kit supports:
- S3 (objects, buckets, lifecycle rules)
- DynamoDB (tables, items, queries)
- SQS (queues, messages)
- Lambda (functions, invocations)
- CloudWatch (logs, metrics)

Performance is excellent for local testing — operations complete in milliseconds rather than seconds. For a typical CI pipeline with 50 AWS service calls, execution time drops from ~30 seconds to under 2 seconds.

## Integration with CI Systems

For your CI workflow, add this to your test runner:

```yaml
# .github/workflows/test.yml
name: Test AWS Integration
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Start local AWS services
      - name: Start Local AWS
        run: |
          docker-compose up -d
          sleep 5  # Wait for services to start
      
      # Run your tests
      - name: Run Tests
        run: |
          pip install boto3 pytest
          pytest test_s3.py -v
      
      # Clean up
      - name: Cleanup
        run: docker-compose down
```

## Common Use Cases

### Testing S3 Operations
```python
# Test file upload/download
def test_s3_operations():
    s3 = boto3.client('s3', endpoint_url='http://localhost:4566')
    
    # Upload file
    s3.put_object(Bucket='my-bucket', Key='test.txt', Body=b'hello world')
    
    # Download and verify
    response = s3.get_object(Bucket='my-bucket', Key='test.txt')
    assert response['Body'].read() == b'hello world'
```

### Testing DynamoDB
```python
# Test table operations
def test_dynamodb():
    dynamodb = boto3.resource('dynamodb', endpoint_url='http://localhost:4566')
    
    # Create table
    table = dynamodb.create_table(
        TableName='test-table',
        KeySchema=[{'AttributeName': 'id', 'KeyType': 'HASH'}],
        AttributeDefinitions=[{'AttributeName': 'id', 'AttributeType': 'S'}],
        BillingMode='PAY_PER_REQUEST'
    )
    
    # Put and get item
    table.put_item(Item={'id': '123', 'data': 'test'})
    response = table.get_item(Key={'id': '123'})
    assert response['Item']['data'] == 'test'
```

## Environment Variables for Different Environments

The Local-AWS Starter Kit supports environment-based configuration:

```bash
# Development (local)
export AWS_ENDPOINT_URL=http://localhost:4566
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test

# Production (real AWS)
export AWS_ENDPOINT_URL=https://s3.amazonaws.com
export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
```

## Testing Best Practices

1. **Isolate tests**: Use unique bucket/table names per test run
2. **Clean up resources**: Delete temporary buckets/tables after testing
3. **Use fixtures**: Set up and tear down services in test fixtures
4. **Mock external dependencies**: Combine with mocking for complete isolation

## Performance Comparison

On a typical development machine:
- Real AWS: 100-500ms per operation (network + service latency)
- Local AWS: <10ms per operation (in-memory execution)
- CI pipeline speed improvement: ~90% reduction in test time

## Get the kit

Ready to start testing locally? The Floci Local-AWS Starter Kit provides everything you need for zero-configuration local AWS development.

[Get the kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)
