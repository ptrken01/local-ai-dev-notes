# AWS Sandbox Local: A Practical Dev Guide

# AWS Sandbox Local: A Practical Dev Guide

When developing AWS applications, you often need to test code that interacts with services like S3, DynamoDB, or Lambda. Running these services locally provides faster iteration, reduced costs, and the ability to work offline.

AWS provides a local development environment called LocalStack, but there's also a newer option: Floci Local-AWS Starter Kit. This tool runs a free, local AWS emulator with zero configuration, allowing you to point your code at it exactly like real AWS.

## Why Use Local AWS Emulators?

Local AWS emulators offer several advantages:
- **Faster feedback loops** during development
- **Cost savings** - no charges for local testing
- **Offline development** capability
- **Consistent testing environment**
- **Easy CI/CD integration**

For developers working with AWS services, having a local emulator is essential for efficient development workflows.

## Getting Started with Floci Local-AWS Starter Kit

The Floci Local-AWS Starter Kit provides a zero-config solution that spins up a complete AWS-compatible environment. Here's how to get it running:

```yaml
# docker-compose.yml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
    environment:
      - AWS_DEFAULT_REGION=us-east-1
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
```

Run with: `docker-compose up -d`

This single command starts a local AWS-compatible service running on port 4566, which is the default for LocalStack compatibility.

## Using the Emulator

Once started, you can interact with your local services using standard AWS SDKs. Here's an example using Python with boto3:

```python
import boto3

# Point to local AWS emulator
s3 = boto3.client(
    's3',
    endpoint_url='http://localhost:4566',
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

# Create bucket
s3.create_bucket(Bucket='my-local-bucket')

# Upload file
s3.put_object(
    Bucket='my-local-bucket',
    Key='test.txt',
    Body=b'Hello Local AWS!'
)

# List objects
response = s3.list_objects_v2(Bucket='my-local-bucket')
print(response['Contents'])
```

The same approach works with other SDKs. For example, in Node.js:

```javascript
const AWS = require('aws-sdk');

const s3 = new AWS.S3({
  endpoint: 'http://localhost:4566',
  accessKeyId: 'test',
  secretAccessKey: 'test',
  region: 'us-east-1'
});

// Create bucket
s3.createBucket({ Bucket: 'my-local-bucket' }, (err, data) => {
  if (err) console.log(err);
  else {
    // Upload file
    s3.putObject({
      Bucket: 'my-local-bucket',
      Key: 'test.txt',
      Body: 'Hello Local AWS!'
    }, (err, data) => {
      console.log('Upload complete');
    });
  }
});
```

## Supported Services

Floci's Local-AWS Starter Kit supports several key AWS services:
- S3 (Object Storage)
- DynamoDB (NoSQL Database)
- SQS (Simple Queue Service)
- SNS (Simple Notification Service)
- Lambda (Serverless Functions)
- CloudFormation (Infrastructure as Code)

This covers most common use cases for local development and testing.

## Real-World Example: Testing a Data Pipeline

Let's say you're building an application that processes data through multiple services:

```python
import boto3
import json

def setup_local_environment():
    """Initialize local AWS services"""
    
    # Setup clients
    s3 = boto3.client('s3', endpoint_url='http://localhost:4566')
    dynamodb = boto3.resource('dynamodb', endpoint_url='http://localhost:4566')
    
    # Create table
    table = dynamodb.create_table(
        TableName='processed-data',
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
    
    # Create bucket
    s3.create_bucket(Bucket='raw-data-bucket')
    
    return s3, table

def process_data(s3_client, table):
    """Process data through local services"""
    
    # Simulate data ingestion
    test_data = {'id': '123', 'name': 'John Doe', 'processed': True}
    
    # Store in S3
    s3_client.put_object(
        Bucket='raw-data-bucket',
        Key='data.json',
        Body=json.dumps(test_data)
    )
    
    # Process and store in DynamoDB
    table.put_item(Item=test_data)
    
    print("Data processed successfully")

# Usage
s3, table = setup_local_environment()
process_data(s3, table)
```

## Performance Characteristics

In practice, local AWS emulators run significantly faster than real AWS services for development tasks. Memory usage typically ranges from 500MB to 1GB depending on the services used. The startup time is usually under 10 seconds.

For typical development workloads, you'll see:
- S3 operations: ~10ms average
- DynamoDB operations: ~20ms average  
- Network latency: ~5ms (local)

## Integration with Development Tools

The emulator integrates well with popular development environments:

**VS Code**: Use AWS Toolkit extension with local endpoint configuration
**IDE Debugging**: Set environment variables in your IDE launch configurations
**CI/CD**: Run tests against local services for faster feedback

Example environment setup for development tools:
```bash
export AWS_ENDPOINT_URL=http://localhost:4566
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test
export AWS_DEFAULT_REGION=us-east-1
```

## Best Practices

1. **Use consistent endpoints**: Always point to `http://localhost:4566` for local development
2. **Environment-based configuration**: Switch between local and real AWS based on environment variables
3. **Data persistence**: Note that data in local emulators is not persistent across restarts (unless you use volumes)
4. **Version compatibility**: Keep your local emulator version aligned with production AWS versions

## Conclusion

Floci Local-AWS Starter Kit provides an excellent solution for developers who need a local AWS environment without the complexity of configuration. With zero setup required, it enables rapid development and testing workflows that mirror real AWS behavior.

The tool supports all major AWS services and integrates seamlessly with existing codebases. Whether you're building serverless applications, data pipelines, or complex distributed systems, having a reliable local AWS emulator is essential for efficient development.

## Get the kit

Ready to try Floci Local-AWS Starter Kit? [Get your free kit now](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002) and start developing locally with zero configuration.
