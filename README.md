# Dokploy Compose Deploy Action

A GitHub Action for deploying Docker Compose applications to Dokploy. This action automates the full deployment process: building and pushing Docker images, updating the compose file, deploying via Dokploy, and optionally cleaning up.

## Features

- **Automated Deployment**: Complete CI/CD workflow for Dokploy Compose apps
- **Docker Integration**: Builds and pushes images to registries
- **Compose Management**: Updates Dokploy with new compose configurations
- **Flexible Configuration**: Customizable paths and optional cleanup

## Prerequisites

- A Dokploy instance with API access
- Dokploy API key with appropriate permissions
- Docker registry access (e.g., Docker Hub, GitHub Container Registry)
- Valid `docker-compose.yml` and `Dockerfile` in your repository

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dokploy_domain` | Dokploy panel domain | Yes | - |
| `api_key` | Dokploy API key | Yes | - |
| `compose_id` | Compose ID in Dokploy | Yes | - |
| `registry_domain` | Docker registry domain (leave empty for DockerHub/local) | No | `""` |
| `registry_username` | Docker registry username | No | - |
| `registry_token` | Docker registry password/token | No | - |
| `image_name` | Image name (repository/name) | Yes | - |
| `tag` | Image tag | No | `latest` |
| `dockerfile_path` | Path to the Dockerfile | No | `Dockerfile` |
| `context_path` | Build context directory (defaults to Dockerfile folder) | No | `""` |
| `compose_path` | Path to docker-compose.yml | No | `docker-compose.yml` |
| `do_clean` | Run cleanAll after deploy | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `deployment_id` | Deployment ID from Dokploy |
| `status` | Deployment status |
| `logs` | Deployment logs |

## Usage

### Basic Deployment

```yaml
name: Deploy to Dokploy

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Deploy with Dokploy
        uses: minisource/dokploy-action@v1
        with:
          dokploy_domain: ${{ secrets.DOKPLOY_DOMAIN }}
          api_key: ${{ secrets.DOKPLOY_API_KEY }}
          compose_id: ${{ secrets.DOKPLOY_COMPOSE_ID }}
          image_name: minisource/your-app
```

### Advanced Configuration

```yaml
- uses: minisource/dokploy-action@v1
  with:
    dokploy_domain: https://my-dokploy-instance.com
    api_key: ${{ secrets.DOKPLOY_API_KEY }}
    compose_id: my-compose-app
    registry_domain: ghcr.io
    registry_username: ${{ github.actor }}
    registry_token: ${{ secrets.GITHUB_TOKEN }}
    image_name: my-org/my-app
    tag: v1.0.0
    dockerfile_path: ./docker/Dockerfile
    context_path: ./docker
    compose_path: ./docker/docker-compose.yml
    do_clean: false
```

## Workflow Steps

The action performs the following steps in order:

1. **Checkout code** (if not already done)
2. **Login to Docker registry** (if credentials provided)
3. **Build Docker image** using the specified Dockerfile
4. **Push image** to the configured registry
5. **Update compose file locally** by replacing the image reference
6. **Update compose in Dokploy** via API
7. **Trigger deployment** in Dokploy
8. **Clean up** (optional) by running cleanAll

## Security

- Store your Dokploy domain and API key as GitHub secrets
- The action does not log sensitive information
- Ensure your Dokploy instance is properly secured
- Use least-privilege API keys

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.