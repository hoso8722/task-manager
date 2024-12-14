# Getting started

## Install Server side development by Laravel sail

### Choosing Your Sail Services

```bash
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

### Install sail on macOS or Linux

```bash
cd example-app
# docker-compose up -d
./vendor/bin/sail up -d

# migrate DB
./vendor/bin/sail artisan migrate
```

Configure A Shell Alias

```bash
alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'
```

To make sure this is always available, you may add this to your shell configuration file in your home directory, such as ~/.zshrc or ~/.bashrc, and then restart your shell.

### Change Configuration

```bash
sail artisan sail:publish
```

Edit `Dockerfile` in ./docker/8.x/ directory

```
# ENV TZ=UTC  comment out or delete line
# ↓ add
ENV TZ='Asia/Tokyo'
```

Rebuild docker images

```bash
sail down
sail build --no-cache
```

Check Timezone in docker shell

```bash
sail up -d
sail shell

date
exit
```

### Configure String code in Mysql

Create `my.cnf` in `./docker/8.x/` directory

Edit `my.cnf`

```my.cnf
[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_bin

[client]
default-character-set = utf8mb4
```

Add line `./docker/8.x/my.cnf:/etc/my.cnf` in `docker-compose.yml`

```docker-compose.yml
services:
    laravel.test:
       # （中略）
    mysql:
        image: 'mysql/mysql-server:8.0'
        ports:
            - '${FORWARD_DB_PORT:-3306}:3306'
        environment:
           # （中略）
        volumes:
            - 'sail-mysql:/var/lib/mysql'
            - './docker/mysql/create-testing-database.sh:/docker-entrypoint-initdb.d/10-create-testing-database.sh'
+            - './docker/8.2/my.cnf:/etc/my.cnf'    # Add line
        networks:
            - sail

```

restart docker-compose

```bash
sail down
sail up -d
sail mysql

mysql > show variables like '%char%';

exit
```

check utf8mb4 values in stdout

```stdout
+--------------------------+--------------------------------+
| Variable_name            | Value                          |
+--------------------------+--------------------------------+
| character_set_client     | utf8mb4                        |
| character_set_connection | utf8mb4                        |
| character_set_database   | utf8mb4                        |
| character_set_filesystem | binary                         |
| character_set_results    | utf8mb4                        |
| character_set_server     | utf8mb4                        |
| character_set_system     | utf8mb3                        |
| character_sets_dir       | /usr/share/mysql-8.0/charsets/ |
+--------------------------+--------------------------------+
8 rows in set (0.01 sec)
```

## Install front end development

#### Install laravel breeze

```bash
sail composer require laravel/breeze --dev
```

#### Install React

Dependencies will be added for a set of packages (react, inertia, etc.) necessary to develop the front end in React.

```bash
sail artisan breeze:install react
```
#### Install packege.json and launch local server

```bash
npm install

npm run dev
```


