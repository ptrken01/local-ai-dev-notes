# Automatisch vs n8n: A Minimal Working Example

When building self-hosted automation workflows, developers often face a critical decision: choose between **Automatisch** and **n8n**. While both platforms offer robust features, their licensing models and deployment strategies diverge significantly.

## The Core Difference

n8n’s fair-code license means that if you run it commercially (even internally), you must purchase a paid license. Automatisch, however, is completely free to use, modify, and deploy — forever. This distinction matters for organizations looking to avoid licensing fees or maintain control over their infrastructure.

Let’s walk through a minimal working example using **Automatisch** with Docker Compose to illustrate how quickly you can get started without any commercial restrictions.

## Minimal Working Example: Email Notification Workflow

Here's a simple workflow that sends an email when a new file is uploaded to a folder:

```yaml
version: '3.8'
services:
  automatisch:
    image: automatisch/automatisch:latest
    container_name: automatisch
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/automatisch
      - SECRET_KEY=your-secret-key-here
    depends_on:
      - db
    volumes:
      - ./data:/app/data

  db:
    image: postgres:15
    container_name: automatisch-db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: automatisch
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

This setup deploys Automatisch with a PostgreSQL database in under 5 minutes. No licensing required, no hidden costs.

## How It Works

1. **Install Docker** and `docker-compose`.
2. Save the above YAML as `docker-compose.yml`.
3. Run: `docker-compose up -d`.
4. Visit [http://localhost:3000](http://localhost:3000) to configure your workflow.

You can now create a workflow that listens for file uploads (e.g., via FTP or S3), and triggers an email notification using SMTP. The entire process is free, private, and fully customizable.

## FAQ

### Q: Is Automatisch really free forever?
Yes. Unlike n8n’s fair-code license which requires commercial use licenses, Automatisch is open-source under MIT and can be used without cost for any purpose — personal or enterprise.

### Q: How does the performance compare to n8n?
Both tools perform similarly for basic workflows. However, Automatisch’s simpler architecture allows faster startup times and easier debugging when running large-scale private deployments.

### Q: What about support and community?
Automatisch has an active GitHub community and a core team maintaining it. While not as extensive as n8n’s ecosystem, the project is stable and well-documented for self-hosted use cases.

## Get It

If you want to dive deeper into self-hosted automation comparisons, including how Automatisch stacks up against other tools, check out this [detailed comparison guide](https://ptrk-en.gumroad.com/l/self-hosted-automation-compared?offer_code=Launch40). It includes real-world deployment strategies and code snippets for advanced setups.

[![Buy the Automation Comparison Guide](https://img.shields.io/badge/Buy-Detailed%20Guide-blue)](https://ptrk-en.gumroad.com/l/self-hosted-automation-compared?offer_code=Launch40)

## Final Thoughts

For developers seeking a fast, private, and free automation solution that doesn’t come with licensing traps, **Automatisch** wins hands down. With Docker Compose, you can deploy a minimal workflow in under 5 minutes — no strings attached.

[Learn more about Automatisch](https://automatisch.io/)