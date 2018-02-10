# AdeoFirst

Use following command to download files:

```sh 
$ git clone https://github.com/Yellioo/adeofirst
```
Start `docker-compose.yml` file (be in the same folder as `docker-compose.yml` file):
```sh
$ sudo docker-compose up
```
> This command will download images, and create containers.
> Also it will create 2 folders. Both of them will be created 
> with root permissions. With next step we will make these files 
> owned by our user. 

Check your UID with this command:
```sh
$ echo $UID
```
Run these commands in terminal (change <your_user_name> to your user name):
```sh
$ sudo docker exec -i adeofirst_php_1 bash -c "cd / && useradd -u <your_user_UID> <your_user_name> && chown -R <your_user_name>:<your_user_name> src/"
$ sudo docker exec -i adeofirst_db_1 bash -c "cd / && useradd -u <your_user_UID> <your_user_name> && chown -R <your_user_name>:<your_user_name> var/lib/mysql"
``` 
`WARNING: AFTER EVERY MYSQL CONTAINER RECREATION, USE SECOND COMMAND AGAIN!!`

Install `composer` in php container with this command:
```sh 
$ docker exec -i adeofirst_php_1 bash -c "cd /src && composer install
```
Change database path in `/src/.env` file. 
Firstly delete of comment old database path, than create new one:
```sh
Old path:
#DATABASE_URL=sqlite:///%kernel.project_dir%/var/data/blog.sqlite
New path:
DATABASE_URL=mysql://user:user@db:3306/db
```
Edit `doctrine.yaml` file which is located in `/src/config/packages`.
Find and edit `"driver"` and `"server_version"` lines, like in example bellow:
```sh
doctrine:
    dbal:
        driver: 'pdo_mysql'
        server_version: '5.6.39'
        charset: utf8mb4
```
Create `/var` folder in `/adeofirst/src` directory.
Update database and load fixtures with these commands:
```sh
$ docker exec -i adeofirst_php_1 bash -c "cd /src && bin/console doctrine:schema:update --force"
$ docker exec -i adeofirst_php_1 bash -c "cd /src && bin/console doctrine:fixtures:load"
```
You can restart your containers with command down bellow:
```sh
$ docker-compose up -d --force-recreate
```
