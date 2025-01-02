# Docker Commands Manual

This manual provides a step-by-step guide for setting up MySQL, a user management web application, and FreeRADIUS server using Docker.

## Prerequisites
- Docker installed on your system.

## Commands

### Step 1: Create Docker Network

```bash
# Create a custom Docker network
docker network create My-network
```

### Step 2: Create Docker Volume for MySQL

```bash
# Create a volume for MySQL data
docker volume create mysql-data
```

### Step 3: Run MySQL Container

```bash
# Run MySQL container
docker run --name mysql-CT --network My-network -e MYSQL_ROOT_PASSWORD=P@ssw0rd@1 -d -p 3306:3306 -v mysql-data:/var/lib/mysql mysql:latest
```

### Step 4: Run User Management Web Application

```bash
# Run the user management web application container
docker run --name userloginmanamentweb --network My-network -p 5001:5001 -p 5002:5002 jetchadaphon/userloginmanamentweb:latest
```

### Step 5: Run FreeRADIUS Server

```bash
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
