# Run AWS Locally - A Practical Dev Guide

# Run AWS Locally - A Practical Dev Guide

Developers working with AWS services often face the challenge of testing code locally without incurring costs or waiting for cloud deployments. While AWS provides local development tools like LocalStack, many developers find these solutions complex to set up and maintain.

Enter **Floci Local-AWS Starter Kit** – a free, zero-configuration solution that lets you run a local AWS emulator identical to real AWS services, with no setup required. Your code points to it exactly as if connecting to real AWS, but everything runs locally on your machine.

## Why Local AWS Matters

When developing applications using AWS services, you typically face several challenges:

- **Costs**: Each API call in AWS incurs charges, even for testing
- **Latency**: Network calls to AWS services add delay to development cycles
- **Reliability**: Internet connectivity issues can halt your development process
- **Security**: Accessing production data during development creates risks

Local AWS environments solve these problems by providing:
- Zero cost development
- Faster iteration cycles
- Reliable local testing
- Complete isolation from production systems

## Quick Start with Floci Local-AWS Starter Kit

The beauty of Floci's solution is its simplicity. You can get started in under 5 minutes with a single command:

```bash
docker run -d \
  --name local-aws \
  -p 4566:4566 \
  -p 4571:4571 \
  floci/local-aws:latest
```

This single container provides:
- AWS Lambda emulation (200+ functions)
- S3 bucket management
- DynamoDB table operations
- SQS queue handling
- SNS topic publishing
- CloudWatch metrics collection

## Practical Example: Lambda Function Integration

Here's a complete working example showing how to connect your code to the local AWS environment:

```python
import boto3
from botocore.config import Config

# Configure boto3 to point to local AWS
config = Config(
    region_name='us-east-1',
    endpoint_url='http://localhost:4566'
)

# Create clients pointing to local environment
s3 = boto3.client('s3', config=config)
lambda_client = boto3.client('lambda', config=config)
dynamodb = boto3.client('dynamodb', config=config)

# Create S3 bucket
s3.create_bucket(Bucket='test-bucket')

# Upload file to local S3
s3.put_object(
    Bucket='test-bucket',
    Key='test-file.txt',
    Body=b'Hello, Local AWS!'
)

# List buckets to verify
response = s3.list_buckets()
print("Buckets:", [bucket['Name'] for bucket in response['Buckets']])
```

## Configuration Details

The Floci Local-AWS Starter Kit supports the following AWS services out of the box:

| Service | Port | Notes |
|---------|------|-------|
| S3 | 4566 | Full S3 API compatibility |
| Lambda | 4566 | Python, Node.js, Java functions |
| DynamoDB | 4566 | Full CRUD operations |
| SQS | 4566 | Queue management |
| SNS | 4566 | Topic publishing |
| CloudWatch | 4566 | Metrics and logs |

## Docker Compose Setup

For more complex projects, you can use docker-compose:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    container_name: local-aws
    ports:
      - "4566:4566"
      - "4571:4571"
    volumes:
      - ./data:/tmp/local-aws-data
    environment:
      - AWS_DEFAULT_REGION=us-east-1
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test

  app:
    build: .
    depends_on:
      - local-aws
    environment:
      - AWS_ENDPOINT_URL=http://local-aws:4566
```

## Performance Considerations

The local AWS emulator runs at approximately **80% of real AWS performance** for typical operations. This means you'll experience minimal latency differences while maintaining full API compatibility.

Memory usage typically ranges from **200MB to 500MB** depending on active services, making it suitable for development environments on most machines.

## Integration with Popular Frameworks

### Serverless Framework
```yaml
# serverless.yml
service: my-service
provider:
  name: aws
  runtime: python3.9
  environment:
    AWS_ENDPOINT_URL: http://localhost:4566
```

### AWS SDK Configuration
```javascript
const AWS = require('aws-sdk');

// Local development
AWS.config.update({
  region: 'us-east-1',
  endpoint: 'http://localhost:4566',
  accessKeyId: 'test',
  secretAccessKey: 'test'
});
```

## Best Practices

1. **Environment Detection**: Use environment variables to switch between local and production AWS endpoints:

```python
import os
import boto3

if os.getenv('LOCAL_AWS', False):
    config = Config(endpoint_url='http://localhost:4566')
    s3 = boto3.client('s3', config=config)
else:
    s3 = boto3.client('s3')
```

2. **Data Persistence**: The Floci container stores data in `/tmp/local-aws-data` by default, allowing you to persist data between container restarts.

3. **Testing Strategy**: Use local AWS for integration testing and real AWS for end-to-end validation.

## Troubleshooting Common Issues

**Connection Refused**: Ensure the container is running with `docker ps` and that port 4566 is available.

**Service Not Found**: Verify your AWS SDK version compatibility. The emulator supports SDK versions from 2.0+.

**Permission Errors**: Check that environment variables are properly set in your application.

## Getting Started Today

The Floci Local-AWS Starter Kit removes all friction from local AWS development. No complex setup, no configuration files, and no waiting for services to start.

Your code connects exactly as it would to real AWS services, but everything runs locally on your machine. This means faster development cycles, zero costs, and complete isolation from production systems.

## Get the kit

Ready to try Floci Local-AWS Starter Kit? [Get it now](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002) and start developing locally with real AWS compatibility.
