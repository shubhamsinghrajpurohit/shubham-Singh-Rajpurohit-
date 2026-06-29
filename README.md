# Shubham Singh Rajpurohit - Node.js App

A Node.js Express application deployed on AWS EC2 with automated GitHub Actions CI/CD pipeline.

## Features

- ✅ Express.js server
- ✅ GitHub Actions for automated deployment
- ✅ AWS EC2 integration
- ✅ Environment configuration
- ✅ Health check endpoint

## Prerequisites

- Node.js 18+
- npm or yarn
- AWS EC2 instance (Ubuntu recommended)
- GitHub account

## Local Setup

1. **Clone the repository:**
```bash
git clone https://github.com/shubhamsinghrajpurohit/shubham-Singh-Rajpurohit-.git
cd shubham-Singh-Rajpurohit-
```

2. **Install dependencies:**
```bash
npm install
```

3. **Create .env file:**
```bash
cp .env.example .env
```

4. **Start the server:**
```bash
npm run dev  # For development with nodemon
# or
npm start   # For production
```

The server will run on `http://localhost:3000`

## API Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check status

## Deployment on AWS EC2

### Step 1: Set Up EC2 Instance

```bash
# Launch an Ubuntu 22.04 EC2 instance
# SSH into your instance
ssh -i your-key.pem ubuntu@your-ec2-public-ip

# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Create app directory
mkdir -p ~/app
cd ~/app

# Clone your repository
git clone https://github.com/shubhamsinghrajpurohit/shubham-Singh-Rajpurohit-.git .

# Install dependencies
npm install

# Start the app (optionally use PM2 for process management)
npm start
```

### Step 2: Configure GitHub Secrets

Add these secrets to your GitHub repository (Settings → Secrets and variables → Actions):

1. **EC2_PRIVATE_KEY** - Your EC2 SSH private key content
2. **EC2_HOST** - Your EC2 public IP address
3. **EC2_USER** - SSH username (usually `ubuntu` for Ubuntu AMI)
4. **EC2_PORT** - SSH port (usually `22`)

### Step 3: Optional - Use PM2 for Process Management

PM2 keeps your app running even after server restart:

```bash
# On your EC2 instance
sudo npm install -g pm2

# Start app with PM2
pm2 start index.js --name "app"

# Enable startup
pm2 startup
pm2 save
```

### Step 4: Optional - Set Up Nginx Reverse Proxy

For production, use Nginx:

```bash
sudo apt install -y nginx

# Create Nginx config
sudo nano /etc/nginx/sites-available/default
```

Add this configuration:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# Test and restart Nginx
sudo nginx -t
sudo systemctl restart nginx
```

## GitHub Actions Workflow

The `.github/workflows/deploy.yml` file automatically:

1. Triggers on push to `main` branch
2. Sets up Node.js environment
3. Installs dependencies
4. Runs tests
5. Deploys to EC2 via SSH
6. Pulls latest code, installs dependencies, and restarts the app

## Testing Deployment

After deployment, test your app:

```bash
curl http://your-ec2-ip:3000/
curl http://your-ec2-ip:3000/health
```

## Troubleshooting

### Deployment fails
- Check if EC2 instance is running
- Verify SSH credentials are correct in GitHub secrets
- Check GitHub Actions logs for detailed errors

### App won't start
- SSH into EC2 and check logs: `pm2 logs` (if using PM2)
- Check if port 3000 is available: `sudo lsof -i :3000`

### Can't connect to app
- Verify security group allows inbound on port 80/3000
- Check if Nginx is running: `sudo systemctl status nginx`

## GitHub Pages (Optional)

For static frontend assets on GitHub Pages:

1. Enable GitHub Pages in repository settings
2. Choose `main` branch and `/docs` folder
3. Add your static files to `/docs` directory

## Contributing

Feel free to fork and contribute!

## License

ISC

## Author

Shubham Singh Rajpurohit
