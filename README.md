# Keycloak Docker Compose for Portainer

This repository contains a Docker Compose configuration for deploying Keycloak with PostgreSQL in Portainer.

## Files Overview

- `docker-compose.yml` - Main Docker Compose configuration
- `env.example` - Environment variables template
- `.env` - Your environment file (create from env.example)

## Quick Start

1. **Setup Environment:**
   ```bash
   cp env.example .env
   # Edit .env file with your desired settings
   ```

2. **Deploy in Portainer:**
   - Go to your Portainer instance
   - Navigate to Stacks
   - Click "Add stack"
   - Upload the `docker-compose.yml` file
   - Click "Deploy the stack"

3. **Configure Nginx Proxy Manager:**
   - Add a new proxy host
   - Domain: `your-domain.com`
   - Scheme: `http`
   - Forward Hostname/IP: `keycloak` (or your Docker host IP)
   - Forward Port: `8081`
   - Enable SSL (recommended)
   - Enable WebSocket Support

4. **Access Keycloak:**
   - URL: `https://your-domain.com`
   - Admin Username: `admin`
   - Admin Password: `admin_password`

## Production Deployment

### 1. Configure Environment

Copy and edit the environment file:

```bash
cp env.example .env
```

Update the `.env` file with production settings:

```env
KEYCLOAK_HOSTNAME=your-domain.com
KEYCLOAK_PORT=8081
KEYCLOAK_HOSTNAME_STRICT=true
KEYCLOAK_COMMAND=start --optimized
POSTGRES_PASSWORD=your_secure_postgres_password
KEYCLOAK_ADMIN_PASSWORD=your_secure_admin_password
```

### 2. Deploy in Portainer

1. Go to your Portainer instance
2. Navigate to Stacks
3. Click "Add stack"
4. Upload the `docker-compose.yml` file
5. Click "Deploy the stack"

## Configuration Options

### Environment Variables

All configuration is done through environment variables. Copy `env.example` to `.env` and modify as needed:

| Variable | Default | Description |
|----------|---------|-------------|
| `KEYCLOAK_VERSION` | `latest` | Keycloak version to use |
| `POSTGRES_VERSION` | `latest` | PostgreSQL version to use |
| `KEYCLOAK_PORT` | `8081` | Port to expose Keycloak |
| `KEYCLOAK_HOSTNAME` | `your-domain.com` | Keycloak hostname (your domain) |
| `KEYCLOAK_COMMAND` | `start-dev` | Keycloak startup command |
| `POSTGRES_PASSWORD` | `keycloak_password` | Database password |
| `KC_BOOTSTRAP_ADMIN_USERNAME` | `admin` | Admin username |
| `KC_BOOTSTRAP_ADMIN_PASSWORD` | `admin_password` | Admin password |

### Development vs Production

| Setting | Development | Production |
|---------|-------------|------------|
| `KEYCLOAK_COMMAND` | `start-dev` | `start --optimized` |
| `KEYCLOAK_HOSTNAME_STRICT` | `true` | `true` |
| `KEYCLOAK_HOSTNAME_STRICT_HTTPS` | `true` | `true` |
| `KEYCLOAK_HOSTNAME` | `your-domain.com` | `your-domain.com` |

## Security Considerations

### Production Checklist

- [ ] Copy `env.example` to `.env`
- [ ] Change default passwords in `.env`
- [ ] Use strong, unique passwords
- [ ] Configure proper hostname
- [ ] Set `KEYCLOAK_COMMAND=start --optimized`
- [ ] Enable HTTPS (recommended)
- [ ] Set up reverse proxy (nginx/traefik)
- [ ] Configure backup strategy
- [ ] Monitor logs and metrics

### Nginx Proxy Manager Configuration

When using Nginx Proxy Manager, configure your proxy host with these settings:

#### Proxy Host Settings:
- **Domain Names**: `your-domain.com`
- **Scheme**: `http`
- **Forward Hostname/IP**: `keycloak` (or your Docker host IP)
- **Forward Port**: `8081`
- **Cache Assets**: `Enabled` (recommended)
- **Block Common Exploits**: `Enabled`
- **Websocket Support**: `Enabled` (required for Keycloak)
- **Client Certificate**: `None` (unless you have client certificates)

#### SSL Settings:
- **SSL Certificate**: Use Let's Encrypt or your own certificate
- **Force SSL**: `Enabled`
- **HTTP/2 Support**: `Enabled`
- **HSTS Enabled**: `Enabled`
- **HSTS Subdomains**: `Enabled`

#### Advanced Settings:
- **Custom Nginx Configuration**: Leave empty (default is fine)
- **Custom Locations**: Not needed for basic setup

## Troubleshooting

### Common Issues

1. **Database Connection Failed**
   - Check if PostgreSQL container is running
   - Verify database credentials
   - Check network connectivity

2. **Keycloak Won't Start**
   - Check logs: `docker logs keycloak`
   - Verify environment variables
   - Ensure PostgreSQL is healthy

3. **Keycloak Won't Start**
   - Wait for initial startup (60-120 seconds)
   - Check if port 8081 is accessible
   - Verify environment variables are set correctly

### Useful Commands

```bash
# Check container status
docker ps

# View logs
docker logs keycloak
docker logs keycloak-postgres

# Access Keycloak container
docker exec -it keycloak /bin/bash

# Check database connection
docker exec -it keycloak-postgres psql -U keycloak -d keycloak
```

## Backup and Restore

### Database Backup

```bash
# Backup
docker exec keycloak-postgres pg_dump -U keycloak keycloak > backup.sql

# Restore
docker exec -i keycloak-postgres psql -U keycloak keycloak < backup.sql
```

### Volume Backup

```bash
# Backup volume
docker run --rm -v keycloak-docker_postgres_data:/data -v $(pwd):/backup alpine tar czf /backup/postgres_backup.tar.gz -C /data .

# Restore volume
docker run --rm -v keycloak-docker_postgres_data:/data -v $(pwd):/backup alpine tar xzf /backup/postgres_backup.tar.gz -C /data
```

## Support

For more information about Keycloak configuration, visit:
- [Keycloak Documentation](https://www.keycloak.org/documentation)
- [Keycloak Docker Images](https://quay.io/repository/keycloak/keycloak)
- [PostgreSQL Docker](https://hub.docker.com/_/postgres) 