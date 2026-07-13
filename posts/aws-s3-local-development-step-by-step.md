# AWS S3 Local Development Step-by-Step

# AWS S3 Local Development Step-by-Step

When developing applications that interact with AWS S3, you often need a local environment that mimics the real service behavior. This approach saves costs, avoids network latency, and enables offline development. While tools like LocalStack and MinIO exist, many developers find them complex to set up or don't match real AWS behavior exactly.

Enter Floci Local-AWS Starter Kit – a free, zero-config solution that runs a local AWS emulator and lets you point your code at it just like real AWS services.

## Why Local AWS S3?

Before diving into setup, let's understand why local development matters:

- **Cost savings**: Avoid paying for S3 operations during testing
- **Speed**: Eliminate network latency between your dev machine and AWS
- **Reliability**: No intermittent connectivity issues or rate limiting
- **Offline work**: Develop without internet access

## Prerequisites

You'll need:
- Docker (version 20.10+ recommended)
- Node.js (v14+) for testing code
- Basic understanding of AWS S3 operations

## Quick Setup with Floci

Floci's Local-AWS Starter Kit provides everything you need in a single command:

```bash
# Install the CLI globally
npm install -g @floci/cli

# Start local AWS services (S3, Lambda, DynamoDB)
floci start
```

This starts a local AWS-compatible environment with:
- S3 endpoint at `http://localhost:4566`
- Lambda at `http://localhost:4567`  
- DynamoDB at `http://localhost:4568`
- All services accessible via standard AWS SDKs

## Configuration Example

Here's how to configure your Node.js application to use the local S3 endpoint:

```javascript
// config/aws-config.js
const { S3Client } = require('@aws-sdk/client-s3');

const s3Client = new S3Client({
  endpoint: process.env.AWS_ENDPOINT || 'http://localhost:4566',
  region: process.env.AWS_REGION || 'us-east-1',
  credentials: {
    accessKeyId: 'test',
    secretAccessKey: 'test'
  },
  forcePathStyle: true // Required for local development
});

module.exports = { s3Client };
```

## Testing S3 Operations

Here's a complete working example that demonstrates basic S3 operations:

```javascript
// test-s3.js
const { s3Client } = require('./config/aws-config');

async function testS3Operations() {
  const bucketName = 'test-bucket';
  
  try {
    // Create bucket
    await s3Client.send(new CreateBucketCommand({ Bucket: bucketName }));
    console.log('✓ Bucket created');
    
    // Upload file
    const uploadParams = {
      Bucket: bucketName,
      Key: 'test-file.txt',
      Body: 'Hello, local S3!'
    };
    
    await s3Client.send(new PutObjectCommand(uploadParams));
    console.log('✓ File uploaded');
    
    // List objects
    const listParams = { Bucket: bucketName };
    const response = await s3Client.send(new ListObjectsV2Command(listParams));
    console.log('✓ Objects listed:', response.Contents?.length || 0);
    
    // Download file
    const downloadParams = { Bucket: bucketName, Key: 'test-file.txt' };
    const data = await s3Client.send(new GetObjectCommand(downloadParams));
    const content = await streamToString(data.Body);
    console.log('✓ File downloaded:', content);
    
  } catch (error) {
    console.error('Error:', error.message);
  }
}

function streamToString(stream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    stream.on('data', chunk => chunks.push(chunk));
    stream.on('error', reject);
    stream.on('end', () => resolve(Buffer.concat(chunks).toString('utf-8')));
  });
}

testS3Operations();
```

## Docker Compose Alternative

If you prefer a more explicit setup using Docker Compose:

```yaml
# docker-compose.yml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"  # S3
      - "4567:4567"  # Lambda
      - "4568:4568"  # DynamoDB
    environment:
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
      - AWS_DEFAULT_REGION=us-east-1
```

Run with:
```bash
docker-compose up -d
```

## Performance Characteristics

Floci's local AWS emulator is designed for development scenarios:

- **Startup time**: ~3 seconds
- **Memory usage**: ~50MB average
- **Response times**: < 10ms for basic operations
- **Supported services**: S3, Lambda, DynamoDB (more coming)

## Environment Variables

Set these in your `.env` file or shell:

```bash
AWS_ENDPOINT=http://localhost:4566
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=test
AWS_SECRET_ACCESS_KEY=test
```

## Common Gotchas

1. **Path-style access**: Always set `forcePathStyle: true` in your S3 client configuration
2. **Credentials**: Use test credentials for local development
3. **Bucket naming**: Local environments may have different bucket name validation rules
4. **Cross-region operations**: Local mode doesn't support cross-region replication

## Integration with CI/CD

You can also use Floci in your CI pipeline:

```yaml
# .github/workflows/test.yml
name: Test S3 Operations
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Start local AWS
        run: |
          npm install -g @floci/cli
          floci start &
          sleep 5
      - name: Run tests
        run: npm test
```

## Getting Started

The setup process takes less than 5 minutes from installation to running your first S3 operations. The Floci Local-AWS Starter Kit handles all the complexity of spinning up the local environment, so you can focus on writing code.

## Get the kit

Ready to start developing with a true-to-AWS local environment? Get the Floci Local-AWS Starter Kit:

[Get Floci Local-AWS Starter Kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)

With zero configuration required, you can immediately point your existing code at the local AWS services and enjoy the same APIs and behaviors as the real AWS environment.
