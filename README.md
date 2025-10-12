# 🐳 Laravel 11 Docker Environment

Production-ready Laravel 11 uchun to'liq Docker muhiti. Local development va VPS deployment uchun tayyor.

---

## 🎯 Nima Uchun Bu Repo?

Bu repository Laravel 11 loyihalaringiz uchun **tayyor Docker muhiti**ni taqdim etadi:

- ✅ **Plug & Play** - Faqat `docker-compose up -d` - hammasi tayyor!
- ✅ **Production Ready** - VPS'ga to'g'ridan-to'g'ri deploy qilish mumkin
- ✅ **Developer Friendly** - Hot reload, detailed logs, easy debugging
- ✅ **Optimized** - Nginx, Redis, PostgreSQL, Meilisearch bilan
- ✅ **Scalable** - Queue workers, scheduler, horizon bilan

---

## 🛠️ Stack

### Core Services
- **PHP 8.3-FPM** - Zamonaviy PHP runtime
- **Nginx** - Optimized web server
- **PostgreSQL 16** - Relational database
- **Redis 7** - Cache, session, queue storage

### Development Tools
- **Meilisearch** - Full-text search engine
- **Mailpit** - Email testing (SMTP + Web UI)
- **Laravel Horizon** - Queue monitoring dashboard

### Process Management
- **Supervisor** - Process manager
- **Queue Workers** - Background job processing
- **Scheduler** - Cron jobs

---

## 📋 System Requirements

```
Docker Engine: 20.10+
Docker Compose: 2.0+
RAM: 4GB minimum
Disk: 10GB free space
```

---

## 🚀 Quick Start

### 1. Clone Repository

```bash
git clone https://github.com/Shoh-27/laravel-docker.git
cd laravel-docker
```

### 2. Laravel Loyihangizni Joylashtiring

```bash
# Agar yangi Laravel loyiha yaratmoqchi bo'lsangiz:
composer create-project laravel/laravel src

# Yoki mavjud loyihani src/ papkasiga ko'chiring
```

### 3. Environment Setup

```bash
# .env faylini nusxalash
cp .env.example .env

# Kerakli o'zgarishlar qiling (ixtiyoriy)
nano .env
```

### 4. Start Docker

```bash
# Build va run
docker-compose up -d --build

# Status check
docker-compose ps
```

### 5. Laravel Setup

```bash
# Container ichiga kirish
docker-compose exec app sh

# Dependencies
composer install
npm install

# Environment
php artisan key:generate
php artisan migrate
php artisan storage:link

# Exit
exit
```

### 6. Dasturga Kirish

Browser'da: **http://localhost:8080**

---

## 🌐 Service URLs

| Service | URL | Description |
|---------|-----|-------------|
| **Laravel App** | http://localhost:8080 | Main application |
| **Mailpit UI** | http://localhost:8025 | Email testing dashboard |
| **Meilisearch** | http://localhost:7700 | Search engine dashboard |
| **Horizon** | http://localhost:8080/horizon | Queue dashboard |
| **PostgreSQL** | localhost:5432 | Database connection |
| **Redis** | localhost:6379 | Cache/Queue connection |

---

## 📦 Docker Services

```yaml
Service       Container           Port    Description
──────────────────────────────────────────────────────────────
app           laravel_app         9000    PHP-FPM application
nginx         laravel_nginx       8080    Web server
db            laravel_postgres    5432    PostgreSQL database
redis         laravel_redis       6379    Redis cache/queue
meilisearch   laravel_meilisearch 7700    Search engine
mailpit       laravel_mailpit     1025    Email testing SMTP
              (mailpit UI)        8025    Email web interface
horizon       laravel_horizon     -       Queue dashboard
queue         laravel_queue       -       Background jobs
scheduler     laravel_scheduler   -       Cron jobs
```

---

## 🔧 Development Commands

### Docker Management

```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# Restart specific service
docker-compose restart nginx

# View logs
docker-compose logs -f app
docker-compose logs -f nginx

# Rebuild containers
docker-compose up -d --build --force-recreate

# Remove everything (⚠️ including volumes)
docker-compose down -v
```

### Laravel Artisan

```bash
# Run artisan commands
docker-compose exec app php artisan [command]

# Common examples:
docker-compose exec app php artisan migrate
docker-compose exec app php artisan db:seed
docker-compose exec app php artisan cache:clear
docker-compose exec app php artisan config:clear
docker-compose exec app php artisan queue:work
docker-compose exec app php artisan tinker
```

### Composer

```bash
# Install packages
docker-compose exec app composer install

# Update packages
docker-compose exec app composer update

# Add new package
docker-compose exec app composer require vendor/package
```

### NPM/Frontend

```bash
# Install dependencies
docker-compose exec app npm install

# Development build
docker-compose exec app npm run dev

# Production build
docker-compose exec app npm run build

# Watch mode
docker-compose exec app npm run dev -- --watch
```

### Database Operations

```bash
# Connect to PostgreSQL
docker-compose exec db psql -U postgres -d laravel

# Database backup
docker-compose exec db pg_dump -U postgres laravel > backup.sql

# Database restore
docker-compose exec -T db psql -U postgres laravel < backup.sql

# View tables
docker-compose exec db psql -U postgres -d laravel -c "\dt"
```

### Redis Operations

```bash
# Connect to Redis CLI
docker-compose exec redis redis-cli

# Test Redis connection
docker-compose exec redis redis-cli ping

# View all keys
docker-compose exec redis redis-cli KEYS "*"

# Flush all data
docker-compose exec redis redis-cli FLUSHALL
```

---

## 📁 Project Structure

```
├── docker-compose.yml          # Docker services configuration
├── Dockerfile                  # PHP/Laravel container image
├── .env.example                # Environment template
├── nginx/
│   └── default.conf           # Nginx configuration
├── supervisor/
│   └── supervisord.conf       # Process manager config
├── docker/
│   └── php/
│       └── php.ini            # PHP settings
└── src/                       # Your Laravel application
    ├── app/
    ├── config/
    ├── database/
    ├── public/
    ├── resources/
    ├── routes/
    └── ...
```

---

## ⚙️ Configuration

### Environment Variables

Docker muhitida `DB_HOST`, `REDIS_HOST` kabi o'zgaruvchilar **container nomlari** bo'lishi kerak:

```env
# Database
DB_CONNECTION=pgsql
DB_HOST=db                    # ← Container name
DB_PORT=5432
DB_DATABASE=laravel
DB_USERNAME=postgres
DB_PASSWORD=secret

# Redis
REDIS_HOST=redis              # ← Container name
REDIS_PORT=6379

# Meilisearch
MEILISEARCH_HOST=http://meilisearch:7700
MEILISEARCH_KEY=masterKey

# Mail (Mailpit)
MAIL_HOST=mailpit             # ← Container name
MAIL_PORT=1025
```

### Port Forwarding

Agar portlar band bo'lsa, `docker-compose.yml`da o'zgartiring:

```yaml
services:
  nginx:
    ports:
      - "8080:80"  # → "8081:80" ga o'zgartirish mumkin
```

---

## 🧪 Testing

### Container Health Check

```bash
# Check all services
docker-compose ps

# Test PostgreSQL
docker-compose exec db psql -U postgres -c "SELECT 1;"

# Test Redis
docker-compose exec redis redis-cli ping

# Test Laravel
docker-compose exec app php artisan --version
```

### Run Laravel Tests

```bash
# PHPUnit tests
docker-compose exec app php artisan test

# Feature tests
docker-compose exec app php artisan test --testsuite=Feature

# With coverage
docker-compose exec app php artisan test --coverage
```

---

## 🐛 Troubleshooting

### Container ishlamasa

```bash
# View logs
docker-compose logs app
docker-compose logs nginx
docker-compose logs db

# Restart service
docker-compose restart app

# Rebuild from scratch
docker-compose down
docker-compose up -d --build
```

### Permission Issues

```bash
# Fix storage permissions
docker-compose exec app chmod -R 775 storage bootstrap/cache
docker-compose exec app chown -R laravel:laravel storage
```

### Database Connection Failed

```bash
# Check database status
docker-compose ps db

# Verify .env settings
cat .env | grep DB_

# Test connection
docker-compose exec app php artisan db:show
```

### Redis Connection Failed

```bash
# Check Redis status
docker-compose ps redis

# Test connection
docker-compose exec redis redis-cli ping
```

### Port Already in Use

```bash
# Find process using port 8080
lsof -i :8080
netstat -ano | findstr :8080  # Windows

# Kill process or change port in docker-compose.yml
```

---

## 🚀 Production Deployment

### 1. VPS Preparation

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install docker.io docker-compose-v2 git -y
sudo systemctl enable docker
sudo systemctl start docker
```

### 2. Deploy Project

```bash
# Clone repository
git clone https://github.com/Shoh-27/laravel-docker.git
cd laravel-docker

# Copy Laravel project to src/
git clone https://github.com/username/laravel-app.git src

# Setup environment
cp .env.example .env
nano .env  # Configure for production
```

### 3. Production Environment

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

# Use strong passwords
DB_PASSWORD=your-strong-password
REDIS_PASSWORD=your-redis-password
```

### 4. Start Services

```bash
# Build and run
docker-compose up -d --build

# Run migrations
docker-compose exec app php artisan migrate --force

# Optimize
docker-compose exec app php artisan config:cache
docker-compose exec app php artisan route:cache
docker-compose exec app php artisan view:cache
```

### 5. SSL Certificate (Let's Encrypt)

```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx -y

# Get certificate
sudo certbot --nginx -d yourdomain.com

# Auto-renewal
sudo systemctl enable certbot.timer
```

---

## 🔒 Security

### Development

- ✅ `.env` faylini **git'ga qo'shmang**
- ✅ Default parollarni ishlatmang
- ✅ `APP_DEBUG=true` faqat local'da

### Production

- [ ] `APP_ENV=production`
- [ ] `APP_DEBUG=false`
- [ ] SSL certificate o'rnating
- [ ] Firewall sozlang (UFW)
- [ ] Strong passwords ishlating
- [ ] Regular backups oling
- [ ] Update container images

```bash
# Firewall setup (Ubuntu)
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

---

## 📊 Performance Optimization

### Laravel Optimization

```bash
# Cache configuration
docker-compose exec app php artisan config:cache
docker-compose exec app php artisan route:cache
docker-compose exec app php artisan view:cache
docker-compose exec app php artisan event:cache

# Composer optimization
docker-compose exec app composer install --optimize-autoloader --no-dev
```

### Database Optimization

```postgresql
-- PostgreSQL'da index qo'shish
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_created_at ON posts(created_at);
```

### Redis Optimization

```env
# .env da
CACHE_PREFIX=myapp_cache
QUEUE_PREFIX=myapp_queue
SESSION_COOKIE=myapp_session
```

---

## 🔄 Backup & Restore

### Database Backup

```bash
# Backup
docker-compose exec db pg_dump -U postgres laravel > backup_$(date +%Y%m%d).sql

# Restore
docker-compose exec -T db psql -U postgres laravel < backup_20250101.sql
```

### Full Backup

```bash
# Backup volumes
docker run --rm -v laravel-docker_postgres_data:/data -v $(pwd):/backup \
  alpine tar czf /backup/postgres_backup.tar.gz /data
```

### Automated Backup Script

```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"

# Database backup
docker-compose exec -T db pg_dump -U postgres laravel > \
  $BACKUP_DIR/db_$DATE.sql

# Keep only last 7 days
find $BACKUP_DIR -name "db_*.sql" -mtime +7 -delete

echo "Backup completed: $DATE"
```

---

## 📚 Documentation

- [Laravel 11 Docs](https://laravel.com/docs/11.x)
- [Docker Docs](https://docs.docker.com)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [Redis Docs](https://redis.io/docs/)
- [Nginx Docs](https://nginx.org/en/docs/)

---

## 🤝 Contributing

Contributions are welcome!

1. Fork the repository
2. Create feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add improvement'`)
4. Push to branch (`git push origin feature/improvement`)
5. Open Pull Request

---

## 📝 Changelog

### v1.0.0 (2025-10-13)
- 🎉 Initial release
- 🐳 Complete Docker environment
- 🔧 PHP 8.3 + Laravel 11
- 🗄️ PostgreSQL 16 + Redis 7
- 🔍 Meilisearch integration
- 📧 Mailpit email testing
- 📊 Laravel Horizon
- ⚙️ Supervisor process management

---

## 👨‍💻 Author

**Your Name**

- GitHub: [@Shoh-27](https://github.com/Shoh-27)
- Email: shohruxn405@gmail.com

---

---

## 🙏 Acknowledgments

- [Laravel](https://laravel.com) - The PHP Framework
- [Docker](https://docker.com) - Container Platform
- [PostgreSQL](https://postgresql.org) - Database
- [Redis](https://redis.io) - Cache/Queue
- [Nginx](https://nginx.org) - Web Server

---

## ⭐ Support

Agar bu repo foydali bo'lsa, ⭐ **star** bering!

---

<div align="center">

**Made with ❤️ for Laravel Developers**

[Report Bug](https://github.com/shoh-27/laravel-docker/issues) · [Request Feature](https://github.com/shoh-27/laravel-docker/issues)

</div>
