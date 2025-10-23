# Step 22 - Production Deployment

## Deployment Strategy

When deploying a NestJS application to production, we need to consider several key components:

1. **Infrastructure**: Setting up a cloud server (EC2)
2. **Runtime Environment**: Node.js and npm
3. **Database**: PostgreSQL for data persistence
4. **Reverse Proxy**: NGINX for load balancing and SSL
5. **Process Management**: PM2 for keeping the app running
6. **Environment Configuration**: Secure environment variables
7. **Database Migrations**: Proper schema management

**Deployment Flow:**
```
Local Development → Git Repository → EC2 Instance → Production
```

## Creating an EC2 Instance

### Step 1: Launch EC2 Instance
1. Go to AWS Console → EC2 Dashboard
2. Click "Launch Instance"
3. Choose **Ubuntu Server 20.04 LTS (Free Tier)**
4. Select **t2.micro** instance type
5. Configure security groups:
   - SSH (Port 22) - Your IP only
   - HTTP (Port 80) - Anywhere
   - HTTPS (Port 443) - Anywhere
   - Custom TCP (Port 3000) - Anywhere (for testing)

### Step 2: Key Pair Setup
```bash
# Download your key pair and set permissions
chmod 400 your-key-pair.pem

# Connect to your instance
ssh -i "your-key-pair.pem" ubuntu@your-ec2-public-ip
```

## Installing Node, NPM, and GIT

### Update System Packages
```bash
sudo apt update
sudo apt upgrade -y
```

### Install Node.js and npm
```bash
# Install Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

### Install Git
```bash
sudo apt install git -y
git --version
```

### Install Build Tools
```bash
sudo apt install build-essential -y
```

## Installing PostgreSQL

### Install PostgreSQL
```bash
sudo apt install postgresql postgresql-contrib -y
```

### Start and Enable PostgreSQL
```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
```

### Configure PostgreSQL
```bash
# Switch to postgres user
sudo -u postgres psql

# Inside PostgreSQL prompt:
CREATE DATABASE nestjs_app;
CREATE USER nestjs_user WITH ENCRYPTED PASSWORD 'your_strong_password';
GRANT ALL PRIVILEGES ON DATABASE nestjs_app TO nestjs_user;
\q
```

### Configure PostgreSQL for Remote Connections
```bash
# Edit postgresql.conf
sudo nano /etc/postgresql/12/main/postgresql.conf

# Find and modify:
listen_addresses = 'localhost'

# Edit pg_hba.conf
sudo nano /etc/postgresql/12/main/pg_hba.conf

# Add line:
local   all             nestjs_user                                md5

# Restart PostgreSQL
sudo systemctl restart postgresql
```

## Installing and Configuring NGINX

### Install NGINX
```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Configure NGINX for NestJS
```bash
sudo nano /etc/nginx/sites-available/nestjs-app
```

**NGINX Configuration:**
```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Enable the Site
```bash
sudo ln -s /etc/nginx/sites-available/nestjs-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## Git Clone and Install

### Clone Your Repository
```bash
cd /home/ubuntu
git clone https://github.com/yourusername/your-nestjs-repo.git
cd your-nestjs-repo
```

### Install Dependencies
```bash
npm install
```

### Build the Application
```bash
npm run build
```

## Create the `.env` File

### Create Production Environment File
```bash
nano .env
```

**Environment Variables:**
```env
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=nestjs_user
DB_PASSWORD=your_strong_password
DB_DATABASE=nestjs_app

# Application Configuration
NODE_ENV=production
PORT=3000

# JWT Configuration
JWT_SECRET=your_super_secret_jwt_key_here
JWT_EXPIRES_IN=1d

# Other API Keys
STRIPE_SECRET_KEY=your_stripe_secret
EMAIL_API_KEY=your_email_api_key
```

### Secure the Environment File
```bash
chmod 600 .env
chown ubuntu:ubuntu .env
```

## Understanding Migrations

### What are Database Migrations?
Migrations are version control for your database schema. They allow you to:
- Track database changes over time
- Apply consistent schema updates across environments
- Rollback changes if needed
- Collaborate with team members safely

### Migration Benefits:
1. **Version Control**: Database schema is versioned
2. **Consistency**: Same schema across all environments
3. **Rollback**: Can undo changes if issues occur
4. **Collaboration**: Team can share schema changes

## Creating and Running Migrations

### Install TypeORM CLI
```bash
npm install -g @nestjs/cli typeorm
```

### Generate Migration
```bash
# Generate migration based on entity changes
npm run typeorm:migration:generate -- --name InitialSchema

# Create empty migration
npm run typeorm:migration:create -- --name AddUserTable
```

### Sample Migration File
```typescript
import { MigrationInterface, QueryRunner } from 'typeorm';

export class InitialSchema1234567890123 implements MigrationInterface {
    name = 'InitialSchema1234567890123'

    public async up(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`
            CREATE TABLE "user" (
                "id" SERIAL NOT NULL,
                "email" character varying NOT NULL,
                "password" character varying NOT NULL,
                "created_at" TIMESTAMP NOT NULL DEFAULT now(),
                CONSTRAINT "PK_user_id" PRIMARY KEY ("id")
            )
        `);
    }

    public async down(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`DROP TABLE "user"`);
    }
}
```

### Run Migrations
```bash
# Run pending migrations
npm run typeorm:migration:run

# Revert last migration
npm run typeorm:migration:revert

# Show migration status
npm run typeorm:migration:show
```

## Testing Migration on EC2

### Verify Database Connection
```bash
# Test database connection
npm run typeorm:query -- "SELECT version();"
```

### Run Migrations on Production
```bash
# Backup database first
sudo -u postgres pg_dump nestjs_app > backup_$(date +%Y%m%d_%H%M%S).sql

# Run migrations
npm run typeorm:migration:run

# Verify tables were created
sudo -u postgres psql -d nestjs_app -c "\dt"
```

### Test Application
```bash
# Start application in production mode
npm run start:prod

# Test API endpoints
curl http://localhost:3000/health
curl http://localhost:3000/api/users
```

## Running with PM2

### Install PM2 Globally
```bash
sudo npm install -g pm2
```

### Create PM2 Ecosystem File
```bash
nano ecosystem.config.js
```

**PM2 Configuration:**
```javascript
module.exports = {
  apps: [{
    name: 'nestjs-app',
    script: 'dist/main.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_file: './logs/combined.log',
    time: true
  }]
};
```

### Start Application with PM2
```bash
# Create logs directory
mkdir logs

# Start with PM2
pm2 start ecosystem.config.js --env production

# Save PM2 configuration
pm2 save

# Setup PM2 to start on boot
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
```

### PM2 Management Commands
```bash
# List running applications
pm2 list

# Monitor applications
pm2 monit

# View logs
pm2 logs nestjs-app

# Restart application
pm2 restart nestjs-app

# Stop application
pm2 stop nestjs-app

# Delete application
pm2 delete nestjs-app
```

### Final Verification
```bash
# Check if app is running
pm2 status

# Test external access
curl http://your-ec2-public-ip

# Check NGINX status
sudo systemctl status nginx

# Check PostgreSQL status
sudo systemctl status postgresql
```

**Deployment Complete! 🚀**

Your NestJS application is now running in production with:
- ✅ Secure EC2 instance
- ✅ Node.js runtime environment
- ✅ PostgreSQL database
- ✅ NGINX reverse proxy
- ✅ Environment configuration
- ✅ Database migrations
- ✅ PM2 process management