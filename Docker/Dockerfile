# Start with a base Ubuntu image
FROM ubuntu:22.04

# Install necessary dependencies
RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    lsb-release \
    software-properties-common \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install MySQL
RUN apt-get update && apt-get install -y \
    mysql-server \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install PostgreSQL
RUN apt-get update && apt-get install -y \
    postgresql \
    postgresql-contrib \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install MongoDB from the official MongoDB repository
RUN mkdir -p /usr/src/mongodb && \
    curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc | gpg --dearmor -o /usr/share/keyrings/mongodb-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/mongodb-archive-keyring.gpg] http://repo.mongodb.org/apt/ubuntu $(lsb_release -cs) mongodb-org 6.0" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list && \
    apt-get update && apt-get install -y \
    mongodb-org \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Redis
RUN apt-get update && apt-get install -y \
    redis-server \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Expose the default ports for MySQL, PostgreSQL, MongoDB, and Redis
EXPOSE 3306 5432 27017 6379

# Configure MySQL
RUN service mysql start && \
    mysql -e "CREATE USER 'root'@'%' IDENTIFIED BY 'rootpassword';" && \
    mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;" && \
    mysql -e "FLUSH PRIVILEGES;"

# Configure PostgreSQL
RUN service postgresql start && \
    sudo -u postgres psql -c "CREATE USER postgres WITH SUPERUSER PASSWORD 'rootpassword';" && \
    sudo -u postgres createdb -O postgres mydatabase

# Configure MongoDB
RUN mkdir -p /data/db && \
    chown -R mongodb:mongodb /data/db && \
    service mongod start && \
    mongo --eval 'db.createUser({user: "root", pwd: "rootpassword", roles:[{role: "root", db: "admin"}]})'

# Configure Redis
RUN sed -i "s/^bind .*/bind 127.0.0.1/" /etc/redis/redis.conf && \
    sed -i "s/^protected-mode .*/protected-mode no/" /etc/redis/redis.conf

# Start all services
CMD service mysql start && \
    service postgresql start && \
    service mongod start && \
    service redis-server start && \
    tail -f /dev/null
