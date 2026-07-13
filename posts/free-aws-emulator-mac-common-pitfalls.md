# Free AWS Emulator Mac Common Pitfalls

# Free AWS Emulator Mac Common Pitfalls

Running a local AWS emulator on macOS can save you significant development time, but it's not without its gotchas. I've encountered these issues while helping developers set up local AWS environments using Floci Local-AWS Starter Kit, and they're worth addressing upfront.

## The Setup Challenge

The most common issue I see is when developers try to run a full AWS stack locally but don't account for macOS-specific networking behaviors. When you spin up local services like DynamoDB, S3, Lambda, and SQS, your local environment needs to properly communicate with them.

Here's what a working `docker-compose.yml` looks like for the starter kit:

```yaml
version: '3.8'
services:
  local-aws:
    image: floci/local-aws:latest
    ports:
      - "4566:4566"  # AWS services port
      - "4571:4571"  # S3 port
    environment:
      - SERVICES=dynamodb,s3,lambda,sqs
      - DEBUG=1
    volumes:
      - ./data:/data
    networks:
      - local-aws-network

networks:
  local-aws-network:
    driver: bridge
```

## Common Pitfall #1: Port Conflicts

The most frequent issue is port conflicts on macOS. If you have other services running, Docker containers might fail to start:

```bash
ERROR: for local-aws  Cannot start service local-aws: driver failed programming external connectivity on endpoint local-aws (hash): Bind for 0.0.0.0:4566 failed: port already in use
```

To check for port usage:
```bash
lsof -i :4566
# or
netstat -an | grep 4566
```

If you find conflicting processes, either kill them or modify your docker-compose ports:
```yaml
ports:
  - "4570:4566"  # Change to unused port
```

## Common Pitfall #2: Environment Variable Issues

The starter kit expects specific environment variables. If you're copying code from AWS documentation, remember that local services often need different endpoints:

```javascript
// Wrong - using AWS defaults
const s3 = new AWS.S3();

// Correct - pointing to local endpoint
const s3 = new AWS.S3({
  endpoint: 'http://localhost:4571',
  accessKeyId: 'test',
  secretAccessKey: 'test',
  region: 'us-east-1'
});
```

## Common Pitfall #3: File System Permissions

Docker on macOS mounts volumes through a VM, which can cause permission issues. If your data directory isn't writable:

```bash
# Check current permissions
ls -la ./data/

# Fix permissions (if needed)
chmod 755 ./data/
```

## Common Pitfall #4: Network Configuration

macOS networking can be tricky with Docker containers. The local-aws service needs to be reachable from your host machine:

```bash
# Verify connectivity
curl http://localhost:4566

# If you get connection refused, check:
docker ps
docker inspect <container-id> | grep -i ip
```

## Common Pitfall #5: Lambda Runtime Limitations

The local Lambda emulator supports only specific runtimes. If your production code uses unsupported languages:

```yaml
# Make sure your lambda function has a supported runtime
environment:
  - AWS_LAMBDA_FUNCTION_RUNTIME=python3.9
```

## Common Pitfall #6: DynamoDB Data Persistence

Many developers expect data to persist between container restarts but forget volume mounting:

```yaml
volumes:
  - ./dynamodb-data:/data/db
```

Without this, you'll lose all tables and data on container restart.

## Testing Your Setup

Here's a minimal test script that verifies your local AWS setup works:

```bash
#!/bin/bash
# test-local-aws.sh

echo "Testing S3 connectivity..."
aws --endpoint-url http://localhost:4571 s3 mb s3://test-bucket

echo "Testing DynamoDB connectivity..."
aws --endpoint-url http://localhost:4566 dynamodb create-table \
  --table-name TestTable \
  --attribute-definitions AttributeName=id,AttributeType=S \
  --key-schema AttributeName=id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST

echo "Setup complete!"
```

## Performance Considerations

The local AWS emulator is resource-intensive. On a MacBook Pro with 16GB RAM:

- Without optimization: 4-8GB memory usage
- With proper docker-compose limits: 2-4GB memory usage

```yaml
# Add resource limits to prevent system slowdowns
services:
  local-aws:
    mem_limit: 4g
    cpus: 1.5
```

## Debugging Tips

Enable debug mode in the starter kit for detailed logs:
```yaml
environment:
  - DEBUG=1
```

Check logs with:
```bash
docker logs <container-id>
# or
docker-compose logs local-aws
```

## Final Checklist

Before diving into development, verify:

- [ ] No port conflicts on 4566, 4571
- [ ] Correct environment variables for endpoints
- [ ] Proper volume mounting for data persistence
- [ ] Correct network configuration
- [ ] Supported Lambda runtime versions

The Floci Local-AWS Starter Kit eliminates most of these issues by providing pre-configured environments that work out-of-the-box. It's the fastest way to get a production-like AWS environment running locally.

## Get the kit

Ready to try it? Get started with the Floci Local-AWS Starter Kit:

[Get the kit - $29](https://buy.stripe.com/cNi5kD3hicEW0rbecE48002)

No credit card required for the free trial.
