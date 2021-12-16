# Docker-Compose-Nginx-WP

```
[root@ip-172-31-44-190 blog]# cat docker-compose.yml
version: "3.9"
services:
  database:
    image: mysql:5.6
    container_name: db
    networks:
      - mynetwork
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: mysqlroot@123
      MYSQL_DATABASE: wpdb
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wp@123
    volumes:
      -  db-data:/var/lib/mysql/
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    networks:
      - mynetwork
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wp@123
      WORDPRESS_DB_NAME: wpdb
    volumes:
      -  wordpress-data:/var/www/html/
  proxy:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: nginx-proxy
    networks:
      - mynetwork
    restart: always
    ports:
      - "80:80"
      - "443:443"
volumes:
  db-data:
  wordpress-data:
networks:
  mynetwork:
[root@ip-172-31-44-190 blog]#
```

```
[root@ip-172-31-44-190 blog]# docker-compose up -d
Creating network "blog_mynetwork" with the default driver
Creating volume "blog_db-data" with default driver
Creating volume "blog_wordpress-data" with default driver
Pulling database (mysql:5.6)...
5.6: Pulling from library/mysql
b4f470726ddc: Pull complete
8ea446d3dd1a: Pull complete
a12b9de3971c: Pull complete
0cece02ee84c: Pull complete
81584261ce18: Pull complete
a511d4a68448: Pull complete
8e2451f19813: Pull complete
c1d341343e37: Pull complete
cde17f57816b: Pull complete
7a542d7e95c6: Pull complete
a06fd3c106ae: Pull complete
Digest: sha256:d0822b1b059e59fef7571b1a96224fb59fb0a84dffa59d8f3859281bde675a85
Status: Downloaded newer image for mysql:5.6
Pulling wordpress (wordpress:latest)...
latest: Pulling from library/wordpress
e5ae68f74026: Pull complete
99c3c1c4d556: Pull complete
2c23b6beb07a: Pull complete
9874148cff9a: Pull complete
52defac98629: Pull complete
585eed6f5399: Pull complete
aa83ee0498ff: Pull complete
356035aff0a0: Pull complete
72fcc4ec7a35: Pull complete
e0903fae56b0: Pull complete
27d2ad3b821d: Pull complete
a3bf4a72a8eb: Pull complete
5066fa6c34bb: Pull complete
8dbc8886c1b4: Pull complete
946eed912c69: Pull complete
575babaf9c25: Pull complete
f021acbc1440: Pull complete
1b72b43701c8: Pull complete
63a9fc19e570: Pull complete
c0704d625394: Pull complete
c476fe1d3674: Pull complete
Digest: sha256:2e848e9501233683b3853c013c24c0cf3af55efb2c9456138dfe9860800b512b
Status: Downloaded newer image for wordpress:latest
Building proxy
Sending build context to Docker daemon  10.75kB
Step 1/5 : FROM nginx
latest: Pulling from library/nginx
e5ae68f74026: Already exists
21e0df283cd6: Pull complete
ed835de16acd: Pull complete
881ff011f1c9: Pull complete
77700c52c969: Pull complete
44be98c0fab6: Pull complete
Digest: sha256:9522864dd661dcadfd9958f9e0de192a1fdda2c162a35668ab6ac42b465f0603
Status: Downloaded newer image for nginx:latest
 ---> f652ca386ed1
Step 2/5 : RUN rm -rf /etc/nginx/default.d/*
 ---> Running in fa87f8977dac
Removing intermediate container fa87f8977dac
 ---> 29ae64cd1be0
Step 3/5 : COPY ./default.conf /etc/nginx/conf.d/default.conf
 ---> 3adaf1123575
Step 4/5 : COPY ./cert.crt /etc/nginx/cert.crt
 ---> 4e6a19050239
Step 5/5 : COPY ./cert.key /etc/nginx/cert.key
 ---> 4d20c44dd353
Successfully built 4d20c44dd353
Successfully tagged blog_proxy:latest
WARNING: Image for service proxy was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating nginx-proxy ... done
Creating wordpress   ... done
Creating db          ... done
[root@ip-172-31-44-190 blog]#
```
