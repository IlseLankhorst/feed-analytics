# Feed Analytics

Analytics platform using Umami with Nginx reverse proxy and SSL certificates.

## Domain

This project is configured for **analytics-feed.nl**

## Setup

### 1. Environment Configuration

Copy the example environment file and fill in your values:

```bash
cp .env.example .env
```

Edit `.env` and set:
- `UMAMI_DB_PASSWORD`: A secure password for the database
- `CERTBOT_EMAIL`: Your email for Let's Encrypt notifications

### 2. DNS Configuration

Configure DNS records at your domain registrar (Hostnet):

- **A record**: `@` → Your server IP address
- **A record**: `www` → Your server IP address (or CNAME: `www` → `@`)

You can verify DNS with:
```bash
dig analytics-feed.nl
dig www.analytics-feed.nl
```

### 3. Start Services

Start the services:

```bash
docker-compose up -d
```

### 4. Initialize SSL Certificates

After DNS has propagated and nginx is running, initialize SSL certificates:

```bash
docker-compose run --rm certbot certonly \
  --webroot \
  --webroot-path=/var/www/certbot \
  --email your-email@example.com \
  --agree-tos \
  --no-eff-email \
  -d analytics-feed.nl \
  -d www.analytics-feed.nl
```

Replace `your-email@example.com` with your actual email address.

### 5. Restart Nginx

After SSL certificates are obtained, restart nginx to load the HTTPS configuration:

```bash
docker-compose restart nginx
```

## Services

- **Nginx**: Reverse proxy on ports 80 (HTTP) and 443 (HTTPS)
- **Umami**: Analytics dashboard on port 3001 (internal)
- **PostgreSQL**: Database for Umami
- **Certbot**: Automatic SSL certificate renewal

## SSL Certificates

SSL certificates are automatically renewed by the certbot container. Certificates are stored in `./nginx/ssl/`.

## Troubleshooting

### Check nginx logs
```bash
docker-compose logs nginx
```

### Check certbot logs
```bash
docker-compose logs certbot
```

### Test nginx configuration
```bash
docker-compose exec nginx nginx -t
```

### Restart services
```bash
docker-compose restart
```


