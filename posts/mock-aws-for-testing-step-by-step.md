# Mock AWS For Testing Step-by-Step

# Mock AWS For Testing Step-by-Step

When developing applications that interact with AWS services, testing can become a complex challenge. You need real AWS credentials, proper permissions, and often incur costs for each API call. This is where local AWS emulators shine – they provide a realistic testing environment without the overhead of cloud resources.

## Why Use Local AWS Emulation?

Local AWS emulation allows you to:
- Test code without incurring AWS charges
- Develop offline without internet connectivity
- Speed up your development cycle
- Avoid rate limiting issues during testing
- Create reproducible test environments

For developers working with AWS services like S3, DynamoDB, Lambda, or SQS, a local emulator provides the exact same API surface as real AWS services.

## Setting Up Floci Local-AWS Starter Kit

The Floci Local-AWS Starter Kit provides a zero-configuration way to run a local AWS environment. You can install it with:

```bash
npm install -g @floci/local-aws
```

Or use Docker without installing anything locally.

## Quick Start with Docker Compose

Here's a minimal `docker-compose.yml` file that sets up the Floci Local-AWS environment:

```yaml
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
    volumes:
      - ./data:/data
```

To start this setup:

```bash
docker-compose up -d
```

This configuration starts the local AWS emulator on port 4566, which is the standard AWS endpoint for most services. The service runs with test credentials and persists data in a local volume.

## Testing Your Code Against Local AWS

Once your local environment is running, update your application code to point at the local endpoint:

```javascript
const AWS = require('aws-sdk');

// Configure SDK to use local AWS endpoint
const s3 = new AWS.S3({
  endpoint: 'http://localhost:4566',
  region: 'us-east-1',
  accessKeyId: 'test',
  secretAccessKey: 'test',
  s3ForcePathStyle: true,
});

// Test basic operations
async function testS3() {
  try {
    // Create a bucket
    await s3.createBucket({ Bucket: 'test-bucket' }).promise();
    
    // Put an object
    await s3.putObject({
      Bucket: 'test-bucket',
      Key: 'test-key',
      Body: 'Hello World'
    }).promise();
    
    // Get the object
    const result = await s3.getObject({
      Bucket: 'test-bucket',
      Key: 'test-key'
    }).promise();
    
    console.log('Object content:', result.Body.toString());
  } catch (error) {
    console.error('Error:', error);
  }
}

testS3();
```

## Supported Services

Floci Local-AWS currently supports:
- S3 (Storage)
- DynamoDB
- SQS
- Lambda
- CloudWatch Logs
- IAM
- SES

The service compatibility is extensive enough for most development and testing scenarios. You can test real AWS SDK calls against these services with minimal code changes.

## Realistic Testing Scenarios

Here's a more comprehensive example that tests multiple operations:

```javascript
const AWS = require('aws-sdk');

const config = {
  endpoint: 'http://localhost:4566',
  region: 'us-east-1',
  accessKeyId: 'test',
  secretAccessKey: 'test',
  s3ForcePathStyle: true,
};

const s3 = new AWS.S3(config);
const dynamodb = new AWS.DynamoDB.DocumentClient(config);

async function comprehensiveTest() {
  try {
    // Test S3
    await s3.createBucket({ Bucket: 'my-test-bucket' }).promise();
    
    const testObject = {
      Bucket: 'my-test-bucket',
      Key: 'test-file.txt',
      Body: 'This is test content'
    };
    
    await s3.putObject(testObject).promise();
    
    const objects = await s3.listObjectsV2({ 
      Bucket: 'my-test-bucket' 
    }).promise();
    
    console.log('Found', objects.Contents.length, 'objects');
    
    // Test DynamoDB
    const tableParams = {
      TableName: 'test-table',
      KeySchema: [
        { AttributeName: 'id', KeyType: 'HASH' }
      ],
      AttributeDefinitions: [
        { AttributeName: 'id', AttributeType: 'S' }
      ],
      BillingMode: 'PAY_PER_REQUEST'
    };
    
    await dynamodb.createTable(tableParams).promise();
    
    const item = {
      TableName: 'test-table',
      Item: {
        id: 'test-id-123',
        name: 'Test Item',
        timestamp: Date.now()
      }
    };
    
    await dynamodb.putItem(item).promise();
    
    const retrieved = await dynamodb.getItem({
      TableName: 'test-table',
      Key: { id: 'test-id-123' }
    }).promise();
    
    console.log('Retrieved item:', retrieved.Item);
    
  } catch (error) {
    console.error('Test failed:', error);
  }
}

comprehensiveTest();
```

## Performance Characteristics

The local emulator has been tested with:
- ~500 concurrent S3 operations per second
- ~200 DynamoDB write operations per second
- Sub-10ms latency for most operations
- Memory usage around 200MB for typical workloads

These performance characteristics make it suitable for CI/CD pipelines and local development environments.

## Common Issues and Solutions

### Connection Problems
If you're having connection issues, verify:
1. The Docker container is running: `docker-compose ps`
2. Port 4566 is accessible: `curl http://localhost:4566`
3. Your application uses the correct endpoint format

### Data Persistence
By default, data persists in a local volume. If you want clean tests:
```bash
docker-compose down -v
docker-compose up -d
```

## Integration with Testing Frameworks

For unit testing, integrate with your existing test runner:

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  setupFilesAfterEnv: ['<rootDir>/test/setup.js']
};
```

```javascript
// test/setup.js
const { execSync } = require('child_process');

// Start local AWS environment before tests
execSync('docker-compose up -d', { stdio: 'ignore' });

// Clean up after all tests
afterAll(() => {
  execSync('docker-compose down', { stdio: 'ignore' });
});
```

## Getting Started with Floci

The Floci Local-AWS Starter Kit eliminates the need for complex configuration. With just one command, you can get a fully functional local AWS environment that mirrors real AWS services.

## Get the kit

Ready to try it out? [Get the Floci Local-AWS Starter Kit](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002) and start testing your AWS applications locally.
