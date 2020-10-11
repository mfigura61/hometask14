# OTUS ДЗ 14 Docker  (Centos 7)
-----------------------------------------------------------------------
### Домашнее задание

1. Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)

2. Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.

3. Ответьте на вопрос: Можно ли в контейнере собрать ядро?

4. Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

5. Создайте кастомные образы nginx и php, объедините их в docker-compose.

6. После запуска nginx должен показывать php info.

7. Все собранные образы должны быть в docker hub.

#### Подготовка.
Установим на рабочую станцию docker и docker-compose.


#### Теоретические вопросы

1. Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.

- Есть несколько объяснений:

  - Образ это слепок, состоящий из слоев файловой системы, причем все слои в образе только на чтение. Когда мы из образа создаем контейнер, то наслаивается еще слой и в него можно записывать. Именно поэтому образ это, грубо говоря, слепок, а экземпляр образа это есть контейнер с возможностью туда записывать что либо.

  - Образ это как исполняемый файл, а контейнер как процесс.

2. Ответьте на вопрос: Можно ли в контейнере собрать ядро?
- гугл подсказывает, что можно собрать ядро, задействовав docker-kernel-builder.


#### Практическая часть

1. Создаем свой кастомный образ на базе alpine

- Создаем отдельную директорию и кладем туда все файлы:

  - Создаем файл Dockerfile (во вложении)

  - Создаем файл index.html с измененным содержимым (этот файл будет импортирован в наш образ при создании) (во вложении)
  - Создаем конфиг nginx.

- После чего собираем наш образ командой ```[mike@mike-dell nginx-alpine]$ sudo docker build -t mf/myimage:nginx .```

- Далее можем запустить наш контейнер командой
```
[mike@mike-dell nginx-alpine]$ sudo docker run -d -p 80:80 e0ec3a4eaf2c
1293c6cdaec64d942070f49b41c3369164d7ac7df729f477261c46f3d2161209
```

- Видим наш образы, их имена и айдишники.:
```
[mike@mike-dell nginx-alpine]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mf/myimage          nginx               e0ec3a4eaf2c        4 minutes ago       6.99MB
mf/dockimage        nginx               ab68f212371d        22 minutes ago      7.03MB
<none>              <none>              ea433b02a96c        32 minutes ago      7.03MB
<none>              <none>              1b952e0aa569        37 minutes ago      218MB
<none>              <none>              8ce33363ce4c        41 minutes ago      218MB
<none>              <none>              c36ee2234c22        51 minutes ago      218MB
alpine              3.12                a24bb4013296        4 months ago        5.57MB
alpine              latest              a24bb4013296        4 months ago        5.57MB
alpine              3.11                f70734b6a266        5 months ago        5.61MB

```
Убедимся, что контейнер запущен.
```
[mike@mike-dell nginx-alpine]$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
1293c6cdaec6        e0ec3a4eaf2c        "nginx -g 'daemon of…"   2 minutes ago       Up About a minute   0.0.0.0:80->80/tcp   bold_albattani

```
И постучимся курлом.

```
[mike@mike-dell nginx-alpine]$ sudo curl http://localhost
<!DOCTYPE html>
<html>
  <head>
      <title>NGINX-ALPINE</title>
  </head>
  <body>
    <h1>Thу custom Docker container is presented.Hello world!</h1>
    <p>...and it works!!!<p>
  </body>
</html>

```

2. Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий

- Ссылка на репозиторий - ```https://hub.docker.com/repository/docker/mike61/nginx```

- Выполняем команду ```docker login```, и вводим логин и пароль от нашего зарегистрированного аккаунта из docker-hub:
```
[mike@mike-dell nginx-alpine]$ sudo docker login
[sudo] пароль для mike:
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: mike61
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

```
- Далее делаем push:
```
[mike@mike-dell nginx-alpine]$ sudo docker tag e0ec3a4eaf2c mike61/nginx
[sudo] пароль для mike:
[mike@mike-dell nginx-alpine]$ sudo docker push  mike61/nginx
The push refers to repository [docker.io/mike61/nginx]
5a7034fd2287: Pushed
8917812b4670: Pushed
42c1787bdf7b: Pushed
beb8fa2573ed: Pushed
50644c29ef5a: Pushed
latest: digest: sha256:c62ec98d57e1ebfe442f349bc2770c4db667d14d6cf8dc1ea6b734fcd85ec617 size: 1360
[mike@mike-dell nginx-alpine]$

```

3. Создайте кастомные образы nginx и php, объедините их в docker-compose (файлы во вложении)

- Переходим туда где у нас храниться файл docker-compose.yml и выполняем:
```
sudo docker-compose up -d
[sudo] пароль для mike:
Creating network "nginx-php_php-app" with the default driver
Pulling web (nginx:)...
latest: Pulling from library/nginx
d121f8d1c412: Pull complete
66a200539fd6: Pull complete
.....
```
проверим, что веб сервер поднялся
```
curl localhost:8080
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.19.3</center>
</body>
</html>
```
