# AWS S3 Local Development - A Practical Dev Guide

# AWS S3 Local Development - A Practical Dev Guide

Developing applications that rely on AWS S3 can be challenging when you're working offline or want to avoid costs during development. This guide shows you how to set up a local S3 environment using Floci Local-AWS Starter Kit, which provides a zero-configuration AWS emulator that works exactly like real AWS.

## Why Local S3 Development?

When building applications with S3 integration, you typically face these pain points:

- **Costs**: Each API call costs money, even during development
- **Network dependency**: You can't develop offline or in low-connectivity environments
- **Testing complexity**: Real AWS resources require proper setup and cleanup

The solution is to use a local S3 emulator that mimics AWS behavior without the costs or network dependencies.

## Setting Up Local S3 with Floci

Floci's Local-AWS Starter Kit provides exactly what you need: a complete, zero-configuration AWS environment that works like real AWS. Here's how to get started:

```yaml
# docker-compose.yml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"
      - "4577:4577"
      - "4578:4578"
    environment:
      - SERVICES=s3,lambda,iam
      - DEBUG=1
```

This minimal configuration gives you a complete AWS development environment with S3 support. The container exposes three ports:
- Port 4566: AWS service endpoints (including S3)
- Port 4577: DynamoDB endpoint  
- Port 4578: SNS endpoint

## Connecting Your Application to Local S3

Once your local environment is running, you can point your application code at it. Here's a practical example using Node.js:

```javascript
// s3-client-setup.js
const { S3Client } = require("@aws-sdk/client-s3");

// Point to local S3
const s3Client = new S3Client({
  region: "us-east-1",
  endpoint: "http://localhost:4566",
  forcePathStyle: true,
  credentials: {
    accessKeyId: "test",
    secretAccessKey: "test"
  }
});

// Test the connection
async function testConnection() {
  try {
    await s3Client.send(new ListBucketsCommand({}));
    console.log("✓ Connected to local S3");
  } catch (error) {
    console.error("✗ Connection failed:", error.message);
  }
}

module.exports = { s3Client, testConnection };
```

## Complete Working Example

Here's a full example showing how to create a bucket and upload a file:

```javascript
// complete-example.js
const { S3Client, CreateBucketCommand, PutObjectCommand, ListObjectsV2Command } = require("@aws-sdk/client-s3");

const s3Client = new S3Client({
  region: "us-east-1",
  endpoint: "http://localhost:4566",
  forcePathStyle: true,
  credentials: {
    accessKeyId: "test",
    secretAccessKey: "test"
  }
});

async function main() {
  const bucketName = "my-local-bucket";
  
  // Create bucket
  await s3Client.send(new CreateBucketCommand({ Bucket: bucketName }));
  console.log("✓ Bucket created");
  
  // Upload file
  await s3Client.send(new PutObjectCommand({
    Bucket: bucketName,
    Key: "test-file.txt",
    Body: "Hello Local S3!"
  }));
  console.log("✓ File uploaded");
  
  // List objects
  const objects = await s3Client.send(new ListObjectsV2Command({ 
    Bucket: bucketName 
  }));
  console.log("Objects:", objects.Contents?.map(obj => obj.Key));
}

main().catch(console.error);
```

## Environment Configuration

For different environments, you can use environment variables:

```bash
# .env file
AWS_ENDPOINT_URL_S3=http://localhost:4566
AWS_ACCESS_KEY_ID=test
AWS_SECRET_ACCESS_KEY=test
AWS_DEFAULT_REGION=us-east-1
```

Your application code then works identically in both local and production environments:

```javascript
// This works the same locally and in production
const s3Client = new S3Client({
  region: process.env.AWS_DEFAULT_REGION,
  endpoint: process.env.AWS_ENDPOINT_URL_S3,
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
  }
});
```

## Performance and Reliability

Floci's Local-AWS Starter Kit runs entirely in Docker containers, providing:

- **Startup time**: Less than 5 seconds for container initialization
- **Memory usage**: ~150MB RAM per service container
- **Network performance**: Near-native performance for S3 operations
- **Persistence**: Data persists between container restarts

## Common Gotchas and Solutions

### Force Path Style
Many SDK clients require path-style addressing:

```javascript
const s3Client = new S3Client({
  forcePathStyle: true, // Required for local development
  endpoint: "http://localhost:4566"
});
```

### CORS Configuration
For web applications, you might need to handle CORS. The emulator supports this through its configuration.

### Authentication
Local environments use test credentials by default. You can customize these in the container environment variables.

## Testing Your Setup

Here's a quick way to verify everything works:

```bash
# Start your local environment
docker-compose up -d

# Test S3 connectivity
aws s3 --endpoint-url http://localhost:4566 ls

# Or test with Node.js
node -e "
const { S3Client } = require('@aws-sdk/client-s3');
const client = new S3Client({ endpoint: 'http://localhost:4566', forcePathStyle: true });
client.listBuckets().then(() => console.log('Success!'));
"
```

## Production Migration

When you're ready to deploy, simply change your endpoint URL:

```javascript
// Development
const endpoint = process.env.NODE_ENV === 'production' 
  ? undefined // Use default AWS endpoint
  : 'http://localhost:4566';

const s3Client = new S3Client({ endpoint, forcePathStyle: true });
```

## Get the kit

Ready to try this for yourself? Get Floci Local-AWS Starter Kit with a free trial and experience local AWS development without any configuration.

[Get the kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)
