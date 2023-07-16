# Docker

1.  Install Docker (https://docs.docker.com/get-docker/)
2.  Start Docker Desktop
3.  In docker-compose.yml for each item in the 'services' section change the
    container_names to something unique and descriptive.
4.  If you have other servers/Docker containers running you may need to adjust the
    ports for the phpmyadmin and wordpress services in docker-compose.yml.

        Note: If needed, you can stop all running Docker containers with:
        docker stop $(docker ps -a -q)

5.  In the same directory as docker-compose.yml run the following command to build
    and automatically start the Docker containers in the background:
    docker-compose up -d
6.  To complete the WordPress installation (creates database, tables, and admin user):
    navigate your web browser to http://localhost:8000 (or whatever port is specified
    for the 'wordpress' service in docker-compose.yml).
7.  To access PHPMyAdmin navigate your web browser to http://localhost:8080 (or whatever)
    port is specified for the 'phpmyadmin' service in docker-compose.yml).

        Server: db:3306
        (this is shown in docker-compose.yml as WORDPRESS_DB_HOST but the port value is
        automatically set by PHPMyAdmin)

        Username: wordpress
        (this is specified in docker-compose.yml as WORDPRESS_DB_USER)

        Password: wordpress
        (this is specified in docker-compose.yml as WORDPRESS_DB_PASSWORD)

## MySQL Database

### MySQL CLI access

Run: docker exec -it mysqlcontainername mysql -u root -p wordpress
(Replace 'mysqlcontainername' with that specified in the db service `container_name` field.
the password is specified in the `WORDPRESS_DB_PASSWORD` field.)

### Importing a Database

When importing an exported database file place the .zip or .sql in ./mysql-import and it
will be located in the mysql container in /tmp. This will allow you to import the database
file by CLI if preferred over PHPMyAdmin:

1. Place the export file ('dbfile.zip' or 'dbfile.sql') in this project's mysql-import/tmp
   directory.
   (This directory is mounted as a volume in the `db` service's filesystem as `/tmp`.)
2. Open up a shell to the `db` container:
   docker exec -it `mysqlcontainername` bash
   (Replace 'mysqlcontainername' with that specified in the db service `container_name` field.)
3. Navigate to the `/tmp` directory within the `db` container:
   `cd /tmp`
4. Verify the database file you wish to import is present:
   `ls -ls`

   This should report back:
   `dbfile.sql`
   (Or whatever the database filename is.)

5. Drop all of the tables in the current wordpress database:
   mysql -u username -p DROP TABLE `wp_commentmeta`, `wp_comments`, `wp_links`, `wp_options`, `wp_postmeta`, `wp_posts`, `wp_termmeta`, `wp_terms`, `wp_term_relationships`, `wp_term_taxonomy`, `wp_usermeta`, `wp_users`;

   When prompted for a password use 'wordpress' or whatever is specified in WORDPRESS_DB_PASSWORD.
   Replace 'username' with 'wordpress' or whatever is specified in WORDPRESS_DB_USER
   in docker-compose.yml.
   Replace `database_name` with 'wordpress' or whatever is specified in MYSQL_DATABASE
   in docker-compose.yml.

6. You can then run the following command to import this database file:
   `mysql -u username -p database_name < databasefile.sql`

   When prompted for a password use 'wordpress' or whatever is specified in WORDPRESS_DB_PASSWORD.
   Replace 'username' with 'wordpress' or whatever is specified in WORDPRESS_DB_USER
   in docker-compose.yml.
   Replace `database_name` with 'wordpress' or whatever is specified in MYSQL_DATABASE
   in docker-compose.yml.
