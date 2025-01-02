# Docker Commands Manual

This manual provides a step-by-step guide for setting up MySQL, a user management web application, and FreeRADIUS server using Docker.

## Prerequisites
- Docker installed on your system.

## Commands

### Linux Version

#### Step 1: Install Docker and Portainer

```bash
# Update package information
sudo apt update

# Install prerequisites
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker's official repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker

# Install Portainer
sudo docker volume create portainer_data
sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

#### Step 2: Create Docker Network

```bash
# Create a custom Docker network
sudo docker network create My-network
```

#### Step 3: Create Docker Volume for MySQL

```bash
# Create a volume for MySQL data
sudo docker volume create mysql-data
```

#### Step 4: Run MySQL Container

```bash
# Run MySQL container
sudo docker run --name mysql-CT --network My-network -e MYSQL_ROOT_PASSWORD=P@ssw0rd@1 -d -p 3306:3306 -v mysql-data:/var/lib/mysql mysql:latest
```

#### Step 5: Run User Management Web Application

```bash
# Run the user management web application container
sudo docker run --name userloginmanamentweb \
  --network My-network \
  -p 5001:5001 -p 5002:5002 \
  -e DB_HOST=mysql-CT \
  -e DB_PORT=3306 \
  -e DB_USER=root \
  -e DB_PASS=P@ssw0rd@1 \
  -e DB_NAME=radius \
  jetchadaphon/userloginmanamentweb:latest
```

#### Step 6: Run FreeRADIUS Server

```bash
# Run FreeRADIUS container
sudo docker run -d -t --name freeradius-server --network My-network -p 1812:1812/udp -p 1813:1813/udp -e DB_HOST=mysql-CT -e DB_PORT=3306 -e DB_USER=root -e DB_PASS=P@ssw0rd@1 -e DB_NAME=radius -e RADIUS_KEY=tec -e RAD_CLIENTS=0.0.0.0 -e RAD_DEBUG=yes 2stacks/freeradius
```

### Windows Version

#### Step 1: Install Docker Desktop and Portainer

1. Download and install Docker Desktop from the [Docker website](https://www.docker.com/products/docker-desktop).
2. Ensure WSL2 is enabled and configured for Docker.
3. Launch Docker Desktop.

#### Step 2: Create Docker Network

```powershell
# Create a custom Docker network
docker network create My-network
```

#### Step 3: Create Docker Volume for MySQL

```powershell
# Create a volume for MySQL data
docker volume create mysql-data
```

#### Step 4: Run MySQL Container

```powershell
# Run MySQL container
docker run --name mysql-CT --network My-network -e MYSQL_ROOT_PASSWORD=P@ssw0rd@1 -d -p 3306:3306 -v mysql-data:/var/lib/mysql mysql:latest
```

#### Step 5: Run User Management Web Application

```powershell
# Run the user management web application container
docker run --name userloginmanamentweb ^
  --network My-network ^
  -p 5001:5001 -p 5002:5002 ^
  -e DB_HOST=mysql-CT ^
  -e DB_PORT=3306 ^
  -e DB_USER=root ^
  -e DB_PASS=P@ssw0rd@1 ^
  -e DB_NAME=radius ^
  jetchadaphon/userloginmanamentweb:latest
```

#### Step 6: Run FreeRADIUS Server

```powershell
# Run FreeRADIUS container
docker run -d -t --name freeradius-server --network My-network -p 1812:1812/udp -p 1813:1813/udp -e DB_HOST=mysql-CT -e DB_PORT=3306 -e DB_USER=root -e DB_PASS=P@ssw0rd@1 -e DB_NAME=radius -e RADIUS_KEY=tec -e RAD_CLIENTS=0.0.0.0 -e RAD_DEBUG=yes 2stacks/freeradius
```

## Notes
1. **Environment Variables**:
   - `DB_HOST`: Hostname of the MySQL container.
   - `DB_PORT`: MySQL port (default: `3306`).
   - `DB_USER`: MySQL root user (default: `root`).
   - `DB_PASS`: Password for the MySQL root user.
   - `DB_NAME`: Database name for RADIUS.
   - `RADIUS_KEY`: Shared secret for RADIUS clients.
   - `RAD_CLIENTS`: Allowed clients (e.g., `0.0.0.0` for all).
   - `RAD_DEBUG`: Set to `yes` for debug mode.

2. **Port Mapping**:
   - MySQL: `3306`.
   - User Management Web App: `5001`, `5002`.
   - FreeRADIUS: `1812/udp`, `1813/udp`.

3. **Volume Mapping**:
   - MySQL data is stored in the Docker volume `mysql-data`.

4. **Custom Network**:
   - Use a custom Docker network for better container isolation and communication.

## References
- [MySQL Docker Hub](https://hub.docker.com/_/mysql)
- [2stacks FreeRADIUS](https://hub.docker.com/r/2stacks/freeradius)
- [Docker Documentation](https://docs.docker.com/)
