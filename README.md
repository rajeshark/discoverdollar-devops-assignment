In this DevOps task, you need to build and deploy a full-stack CRUD application using the MEAN stack (MongoDB, Express, Angular 15, and Node.js). The backend will be developed with Node.js and Express to provide REST APIs, connecting to a MongoDB database. The frontend will be an Angular application utilizing HTTPClient for communication.  

The application will manage a collection of tutorials, where each tutorial includes an ID, title, description, and published status. Users will be able to create, retrieve, update, and delete tutorials. Additionally, a search box will allow users to find tutorials by title.

Phase 1: Repository Setup
Step 1: Clone and Explore
bash
# Clone the repository
git clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name

# Explore project structure
ls -la
Step 2: Understand the Codebase
Frontend: Angular application in /frontend

Backend: Node.js/Express API in /backend

Configuration: Docker and deployment files in root

Phase 2: Local Development Setup
Step 3: Backend Setup
bash
cd backend

# Install dependencies
npm install

# Create environment file
cp .env.example .env

# Edit environment variables
nano .env
Add to .env:

env
NODE_ENV=development
PORT=3000
MONGODB_URI=mongodb://localhost:27017/crud-app
Step 4: Frontend Setup
bash
cd frontend

# Install dependencies
npm install

# Build for production
npm run build
Step 5: Test Locally
bash
# Terminal 1 - Start backend
cd backend
npm start

# Terminal 2 - Start frontend
cd frontend
ng serve

# Access at: http://localhost:4200
Phase 3: Docker Containerization
Step 6: Build Docker Images
bash
# Build backend image
docker build -t yourusername/mean-backend ./backend

# Build frontend image
docker build -t yourusername/mean-frontend ./frontend

# Or build all with compose
docker-compose build
Step 7: Test Docker Containers Locally
bash
# Start all services
docker-compose up -d

# Check running containers
docker ps

# Expected output:
# CONTAINER ID   IMAGE          PORTS                   NAMES
# abc123         mean-frontend  0.0.0.0:80->80/tcp      frontend
# def456         mean-backend   0.0.0.0:3000->3000/tcp  backend
# ghi789         mongo          0.0.0.0:27017->27017/tcp mongodb

# View logs
docker-compose logs -f
Step 8: Push to Docker Hub
bash
# Login to Docker Hub
docker login

# Tag images
docker tag mean-backend yourusername/mean-backend:latest
docker tag mean-frontend yourusername/mean-frontend:latest

# Push to registry
docker push yourusername/mean-backend:latest
docker push yourusername/mean-frontend:latest
Phase 4: Cloud Infrastructure Setup
Step 9: Create Cloud VM (AWS EC2)
Launch EC2 Instance

OS: Ubuntu 20.04 LTS

Instance Type: t2.micro (free tier)

Storage: 8GB SSD

Configure Security Group

bash
# Open ports:
- SSH: 22
- HTTP: 80
- Backend API: 3000
- MongoDB: 27017
SSH into Instance

bash
ssh -i your-key.pem ubuntu@your-ec2-ip-address
Step 10: Install Docker on VM
bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add user to docker group
sudo usermod -aG docker ubuntu

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker --version
docker-compose --version

# Logout and login again for group changes
exit
ssh -i your-key.pem ubuntu@your-ec2-ip-address
Phase 5: Production Deployment
Step 11: Deploy Application on VM
bash
# Clone your repository
git clone https://github.com/yourusername/your-repo-name.git
cd your-repo-name

# Create production environment file
nano .env.production
Add production variables:

env
NODE_ENV=production
MONGODB_URI=mongodb://mongodb:27017/crud-app
Step 12: Start Production Services
bash
# Deploy with Docker Compose
docker-compose up -d

# Verify all services are running
docker ps

# Check service health
curl http://localhost:3000/health
curl http://localhost:80
Step 13: Configure Nginx Reverse Proxy
bash
# Verify nginx configuration
docker exec frontend nginx -t

# Check nginx is listening on port 80
sudo netstat -tulpn | grep :80

# Test reverse proxy routing
curl http://localhost/api/health
curl http://localhost/
Phase 6: CI/CD Pipeline Setup
Step 14: Configure GitHub Actions
Create .github/workflows/deploy.yml:

yaml
name: Deploy MEAN Stack

on:
  push:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Build Docker images
      run: |
        docker-compose build
        
    - name: Run tests
      run: |
        docker-compose up -d
        docker-compose exec backend npm test

  deploy-production:
    needs: build-and-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - name: Deploy to production server
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USER }}
        key: ${{ secrets.SERVER_SSH_KEY }}
        script: |
          cd /home/ubuntu/your-repo-name
          git pull origin main
          docker-compose down
          docker-compose pull
          docker-compose up -d
Step 15: Set GitHub Secrets
Go to your GitHub repository

Settings → Secrets and variables → Actions

Click New repository secret

Add these secrets:

SERVER_HOST: Your EC2 public IP

SERVER_USER: ubuntu

SERVER_SSH_KEY: Your private SSH key

✅ Verification Checklist
Application Verification
Frontend accessible at http://your-server-ip

Backend API responding at http://your-server-ip:3000/api

MongoDB connected and responsive

All Docker containers running without errors

Infrastructure Verification
Nginx routing traffic correctly

Port 80 accessible from internet

Security groups properly configured

Docker services auto-start on reboot

CI/CD Verification
GitHub Actions workflow executing on push

Automated builds successful

Deployment to production working

Containers restarting with new images

🐛 Troubleshooting Guide
Common Issues & Solutions
1. Port Already in Use
bash
# Find process using port
sudo lsof -i :80
sudo lsof -i :3000

# Kill process
sudo kill -9 <PID>
2. Docker Build Failures
bash
# Clean build cache
docker system prune -a

# Build with no cache
docker-compose build --no-cache
3. MongoDB Connection Issues
bash
# Check if MongoDB is running
docker exec mongodb mongo --eval "db.adminCommand('ismaster')"

# Check connection from backend
docker-compose exec backend node -e "console.log('Testing DB connection')"
4. Container Networking Problems
bash
# Check network
docker network ls
docker network inspect your-repo-name_mean-network

# Restart services
docker-compose down
docker-compose up -d
5. Nginx Configuration Errors
bash
# Test nginx config
docker exec frontend nginx -t

# View nginx logs
docker-compose logs frontend
Reset Everything
bash
# Complete environment reset
docker-compose down -v --remove-orphans
docker system prune -a
docker volume prune
docker-compose up -d --build
