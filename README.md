# XAMPP Docker Setup

This project provides a Docker setup for running XAMPP with Docker Compose.

## Prerequisites

Ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Getting Started

### Build and Start the Container

For the first-time setup, build the Docker image and start the container:

```bash
docker compose -f docker-compose.yml up --build
```

To start the container without rebuilding the image:

```bash
docker compose -f docker-compose.yml up
```

### Configuration

- **Image**: `fredblgr/ubuntu-xampp:2020`
- **Host Directory Mapping**: Maps `./your-host-directory` to `/opt/lampp/htdocs` in the container.

## MariaDB Remote Access Configuration

To configure MariaDB for remote access, follow these steps:

1. **Access the Container**

   Enter the XAMPP container:

   ```bash
   docker exec -it xampp_container /bin/bash
   ```

2. **Log into MariaDB**

   Connect to MariaDB:

   ```bash
   /opt/lampp/bin/mysql -u root -p
   ```

   Enter the MariaDB root password.

3. **Grant Remote Access**

   Execute the following SQL commands to grant access to all IP addresses:

   ```sql
   GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_root_password' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   ```

   Replace `your_root_password` with the actual password.

4. **Update MariaDB Configuration (if necessary)**

   Edit the MariaDB configuration file (`/opt/lampp/etc/my.cnf` or `/opt/lampp/etc/my.cnf.d/`) and set `bind-address` to `0.0.0.0`:

   ```ini
   [mysqld]
   bind-address = 0.0.0.0
   ```

   Restart MariaDB to apply changes:

   ```bash
   /opt/lampp/lampp restart
   ```

5. **Verify Docker Compose Configuration**

   Ensure your `docker-compose.yml` correctly exposes the MariaDB port:

   ```yaml
   version: '3'

   services:
     xampp:
       image: fredblgr/ubuntu-xampp:2020
       ports:
         - "8091:80"
         - "443:443"
         - "3306:3306"
       container_name: xampp_container
       volumes:
         - ./your-host-directory:/opt/lampp/htdocs
   ```

6. **Test the Connection**

   Verify remote connection:

   ```bash
   mysql -u root -p -h 127.0.0.1 -P 3306
   ```

   Replace `127.0.0.1` with the Docker container's IP if needed.

## Stopping the Container

To stop and remove the running container:

```bash
docker compose -f docker-compose.yml down
```

## Additional Resources

- [XAMPP Documentation](https://www.apachefriends.org/index.html)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## Troubleshooting

If you face issues:

- Verify Docker and Docker Compose installations.
- Check for port mapping conflicts.
- Consult Docker logs for detailed error messages.

