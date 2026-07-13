# Docker Aws Emulator Docker-First Walkthrough

# Docker Aws Emulator Docker-First Walkthrough

When developing AWS applications locally, you often need a reliable emulator that mimics real AWS services without incurring costs or requiring internet connectivity. The Floci Local-AWS Starter Kit provides exactly this — a zero-configuration local AWS emulator that works seamlessly with your existing code.

## Why Use a Local AWS Emulator?

Before diving into setup, let's understand why local emulators matter for developers:

- **Cost control**: No AWS charges during development
- **Faster iteration**: Eliminates network latency between local and cloud
- **Offline development**: Work without internet connectivity
- **Consistent testing**: Reproducible environments across teams

The Floci kit delivers all these benefits with minimal configuration, making it ideal for developers working with AWS services like S3, DynamoDB, Lambda, and more.

## Getting Started with Floci Local-AWS Starter Kit

The beauty of this solution lies in its simplicity. You don't need to configure individual services or manage complex Docker setups — everything works out of the box.

### Quick Setup Example

Here's a minimal `docker-compose.yml` file that gets you running:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
    environment:
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
      - AWS_DEFAULT_REGION=us-east-1
```

This single configuration file spins up a complete local AWS ecosystem on port 4566. The container exposes all standard AWS endpoints and handles service discovery automatically.

### Running the Emulator

With Docker Compose installed, simply run:

```bash
docker-compose up -d
```

The emulator will be available at `http://localhost:4566` within seconds. You can verify it's running by checking if the status endpoint responds:

```bash
curl http://localhost:4566/health
```

This returns a JSON response confirming all services are operational, typically within 3-5 seconds of starting the container.

## Integration with Your Existing Code

The magic happens when you point your existing AWS SDK code at this local environment. Here's how to modify typical Python code:

```python
import boto3

# Local endpoint configuration
endpoint_url = 'http://localhost:4566'

# S3 client pointing to local emulator
s3 = boto3.client(
    's3',
    endpoint_url=endpoint_url,
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

# Create bucket
s3.create_bucket(Bucket='my-local-bucket')

# Upload object
s3.put_object(
    Bucket='my-local-bucket',
    Key='test-file.txt',
    Body=b'Hello, local AWS!'
)
```

The same pattern works for other services. For DynamoDB:

```python
dynamodb = boto3.resource(
    'dynamodb',
    endpoint_url=endpoint_url,
    aws_access_key_id='test',
    aws_secret_access_key='test',
    region_name='us-east-1'
)

table = dynamodb.create_table(
    TableName='my-table',
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
    ]
)
```

## Service Coverage and Performance

The Floci Local-AWS Starter Kit includes support for:

- S3-compatible storage (98% compatibility with real AWS S3)
- DynamoDB (full CRUD operations)
- Lambda functions (with Docker-based execution)
- SQS and SNS messaging services
- CloudWatch logging

Performance metrics show that typical operations complete in under 50ms, which is comparable to real AWS latency for local requests. The emulator handles approximately 1000 concurrent connections reliably.

## Common Configuration Options

While the default setup works immediately, you might want to customize certain aspects:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
    environment:
      # Override default credentials
      - AWS_ACCESS_KEY_ID=local-key
      - AWS_SECRET_ACCESS_KEY=local-secret
      # Set specific region
      - AWS_DEFAULT_REGION=us-west-2
      # Enable debug logging
      - DEBUG=true
    volumes:
      # Persist data between restarts
      - ./data:/data
```

## Troubleshooting Tips

If you encounter issues, the most common problems stem from port conflicts or network configuration:

1. **Port already in use**: Change the host port mapping
2. **Connection refused**: Verify the container is running with `docker ps`
3. **Credential errors**: Check environment variables match your code

## When to Use This Approach

This solution works best for:

- Development environments where real AWS costs are undesirable
- CI/CD pipelines that need consistent testing infrastructure
- Teams wanting to reduce network dependency
- Developers working offline or in restricted networks

The Floci Local-AWS Starter Kit excels when you want a production-like environment without the overhead of managing multiple services.

## Get the kit

Ready to try the Floci Local-AWS Starter Kit? Start with zero configuration and point your code at it exactly like real AWS. 

**[Get the kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)**
