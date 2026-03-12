# Vibe Kanban - Coolify Deployment Guide

This repository is configured for easy deployment on Coolify with auto-configuration of environment variables.

## Quick Deployment on Coolify

### 1. Fork or Clone Repository
- Fork this repository or use the `o3c-custom` branch directly
- This branch contains all necessary Docker configuration files for Coolify

### 2. Create New Application in Coolify
1. Go to your Coolify dashboard
2. Create a new **Docker Compose** application
3. Connect your Git repository (select the `o3c-custom` branch)
4. Set **Build Pack** to `docker-compose`

### 3. Configure Environment Variables

In your Coolify application, set the following required environment variables:

#### Required Variables
```env
# Your domain (auto-configured by Coolify, but verify)
SERVICE_FQDN=your-domain.com

# Email for SSL certificates
ACME_EMAIL=admin@your-domain.com

# Strong JWT secret (generate with: openssl rand -base64 48)
VIBEKANBAN_REMOTE_JWT_SECRET=your-secure-jwt-secret-here

# Database credentials (use secure passwords)
POSTGRES_PASSWORD=your-secure-db-password
ELECTRIC_ROLE_PASSWORD=your-secure-electric-password

# OAuth Provider (configure at least one)
GITHUB_OAUTH_CLIENT_ID=your-github-client-id
GITHUB_OAUTH_CLIENT_SECRET=your-github-client-secret
```

#### OAuth Configuration

**GitHub OAuth:**
1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. Create a new OAuth app
3. Set Authorization callback URL: `https://your-domain.com/v1/oauth/github/callback`

**Google OAuth:**
1. Go to [Google Cloud Console](https://console.cloud.google.com/apis/credentials)
2. Create OAuth 2.0 credentials
3. Set Authorized redirect URI: `https://your-domain.com/v1/oauth/google/callback`

### 4. Deploy
1. Click **Deploy** in Coolify
2. Wait for the build to complete (first build takes 10-15 minutes)
3. Access your Vibe Kanban instance at your configured domain

## Architecture

This deployment includes:

- **Traefik**: Reverse proxy with automatic SSL certificates (Let's Encrypt)
- **PostgreSQL 16**: Database with logical replication enabled
- **ElectricSQL**: Real-time sync service
- **Azurite**: Local Azure Blob Storage emulator
- **Vibe Kanban**: Main application server

## Storage

- **Database**: Persistent PostgreSQL data
- **File Storage**: Local Azurite container (mimics Azure Blob Storage)
- **SSL Certificates**: Automatically managed by Traefik

## Customization

### Using External Storage
To use Azure Blob Storage or Cloudflare R2 instead of local Azurite:

1. Set the appropriate environment variables in Coolify:
   ```env
   # For Azure Blob Storage
   AZURE_STORAGE_ACCOUNT_NAME=your-account
   AZURE_STORAGE_ACCOUNT_KEY=your-key
   AZURE_STORAGE_CONTAINER_NAME=your-container
   
   # For Cloudflare R2
   R2_ACCESS_KEY_ID=your-access-key
   R2_SECRET_ACCESS_KEY=your-secret-key
   R2_REVIEW_ENDPOINT=your-endpoint
   R2_REVIEW_BUCKET=your-bucket
   ```

### Email Integration
To enable invitation emails:
```env
LOOPS_EMAIL_API_KEY=your-loops-api-key
```

### GitHub App Integration
For enhanced repository features:
```env
GITHUB_APP_ID=your-app-id
GITHUB_APP_PRIVATE_KEY=your-private-key
GITHUB_APP_WEBHOOK_SECRET=your-webhook-secret
GITHUB_APP_SLUG=your-app-slug
```

## Monitoring

Access your deployment logs in Coolify dashboard or via CLI:
```bash
# View all services
coolify logs

# View specific service
coolify logs vibe-kanban
```

## Troubleshooting

### Common Issues

1. **OAuth Redirect Mismatch**: Ensure your OAuth callback URLs match your domain
2. **Database Connection**: Check that `POSTGRES_PASSWORD` and `ELECTRIC_ROLE_PASSWORD` are set correctly
3. **SSL Issues**: Verify `ACME_EMAIL` is valid and ports 80/443 are accessible

### Health Checks
The application includes health checks for all services:
- PostgreSQL: Database connectivity
- ElectricSQL: Service health
- Vibe Kanban: HTTP health endpoint
- Azurite: Storage availability

### Backup
Database backups can be automated in Coolify or manually executed:
```bash
# Manual backup
docker exec vibe-kanban-postgres-1 pg_dump -U remote remote > backup.sql
```

## Security Notes

- All passwords and secrets should be securely generated
- OAuth credentials must be kept confidential
- JWT secret should be unique per deployment
- Regular security updates are recommended

For additional support, refer to the [official Vibe Kanban documentation](https://vibekanban.com/docs) or [Coolify documentation](https://coolify.io/docs).