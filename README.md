# microservices
# Homework 14 Docker-1

- [Install Docker on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

Версия docker:
```
docker version
```
Список запущенных контейнеров:
```
docker ps
```
Список всех контейнеров:
```
docker ps -a
```
Список сохранненных образов
```
docker images
```

start запускает остановленный (уже созданный) контейнер:
```
docker start container_id
```
attach подсоединяет терминал к созданному контейнеру:
```
docker attach container_id
```

Docker run
```
• Через параметры передаются лимиты(cpu/mem/disk), ip, volumes
• -i – запускает контейнер в foreground режиме (docker attach)
• -d – запускает контейнер в background режиме
• -t создает TTY
• docker run -it ubuntu:16.04 bash
• docker run -dt nginx:latestИзбавляем бизнес от ИТ-зависимости
```

Docker exec
```
• Запускает новый процесс внутри контейнера
• Например, bash внутри контейнера с приложением
```
Docker commit
```
• Создает image из контейнера
• Контейнер при этом остается запущенным
```
Docker kill & stop
```
• kill сразу посылает SIGKILL
• stop посылает SIGTERM, и через 10 секунд(настраивается) посылает SIGKILL
• SIGTERM - сигнал остановки приложения
• SIGKILL - безусловное завершение процесса
```
Docker kill
```
docker ps -q
docker kill $(docker ps -q)
```

docker system df
```
• Отображает сколько дискового пространства занято образами, контейнерами и volume’ами
• Отображает сколько из них не используется и возможно удалить
```
Docker rm & rmi
```
• rm удаляет контейнер, можно добавить флаг -f, чтобы удалялся работающий container(будет послан sigkill)
• rmi удаляет image, если от него не зависят запущенные контейнеры
```
```
docker rm $(docker ps -a -q) # удалит все незапущенные контейнеры
docker rmi $(docker images -q)
```
# Homework 15 Docker-2

- [Docker Documentation](https://docs.docker.com/)
- [Docker на bash](https://github.com/p8952/bocker)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

Docker machine:
- [Linux](https://docs.docker.com/machine/install-machine/)
```
• docker-machine - встроенный в докер инструмент для создания хостов и установки на них docker engine. Имеет поддержку облаков и систем виртуализации (Virtualbox, GCP и др.)

• Команда создания - docker-machine create <имя>. Имен может быть много, переключение между ними через eval $(docker-machine env <имя>). Переключение на локальный докер - eval $(docker-machine env --unset). Удаление - docker-machine rm <имя>.

• docker-machine создает хост для докер демона со указываемым образом в --googlemachine-image, в ДЗ используется ubuntu-16.04. Образы которые используются для построения докер контейнеров к этому никак не относятся.

• Все докер команды, которые запускаются в той же консоли после eval $(docker-machine env <имя>) работают с удаленным докер демоном в GCP.
```
Проверяем, что наш Docker-хост успешно создан:
```
$ docker-machine ls
```
и начинаем с ним работу
```
$ eval $(docker-machine env docker-host)
```

Dockerfile:
```
# За основу возьмем дистрибутив ubuntu версии 16.04
FROM ubuntu:16.04

# Для работы приложения нам нужны mongo и ruby
# Обновим кеш репозитория и установим нужные пакеты
RUN apt-get update
RUN apt-get install -y mongodb-server ruby-full ruby-dev build-essential git
RUN gem install bundler
# Скачаем наше приложение в контейнер
RUN git clone -b monolith https://github.com/express42/reddit.git

# Скопируем файлы конфигурации в контейнер
COPY mongod.conf /etc/mongod.conf
COPY db_config /reddit/db_config
COPY start.sh /start.sh

# Теперь нам нужно установить зависимости приложения и произвести настройку
RUN cd /reddit && bundle install
RUN chmod 0777 /start.sh

# Добавляем старт сервиса при старте контейнера
CMD ["/start.sh"]
```
Теперь мы готовы собрать свой образ
```
$ docker build -t reddit:latest .
```
```
• Точка в конце обязательна, она указывает на путь до Docker-контекста
• Флаг -t задает тег для собранного образа
```
Теперь можно запустить наш контейнер
командой:
```
$ docker run --name reddit -d --network=host reddit:latest
$ docker-machine ls
```
Загрузим наш образ на docker hub для использования в будущем:
```
$ docker tag reddit:latest <your-login>/otus-reddit:1.0
$ docker push <your-login>/otus-reddit:1.0
```
Проверка: 

Выполним в другой консоли:
```
$ docker run --name reddit -d -p 9292:9292 <your-login>/otus-reddit:1.0
```
Дополнительно можете с помощью следующих команд изучить логи контейнера, зайти в выполняемый контейнер, посмотреть список процессов, вызвать остановку контейнера, запустить его повторно, остановить и удалить, запустить контейнер без запуска приложения и посмотреть процессы:
```
• docker logs reddit -f
• docker exec -it reddit bash
• ps aux
• killall5 1
• docker start reddit
• docker stop reddit && docker rm reddit
• docker run --name reddit --rm -it <your-login>/otus-reddit:1.0 bash
• ps aux
• exit
```
И с помощью следующих команд можно посмотреть подробную информацию о образе, вывести только определенный фрагмент информации, запустить приложение и добавить/удалить папки и посмотреть дифф, проверить что после остановки и удаления контейнера никаких изменений не останется:
```
• docker inspect <your-login>/otus-reddit:1.0
• docker inspect <your-login>/otus-reddit:1.0 -f '{{.ContainerConfig.Cmd}}'
• docker run --name reddit -d -p 9292:9292 <your-login>/otus-reddit:1.0
• docker exec -it reddit bash
• mkdir /test1234
• touch /test1234/testfile
• rmdir /opt
• exit
• docker diff reddit
• docker stop reddit && docker rm reddit
• docker run --name reddit --rm -it <your-login>/otus-reddit:1.0 bash
• ls /
```

# Homework 16 Docker-3

- [DB per Service](https://microservices.io/patterns/data/database-per-service.html)
- [Методология для создания сервисных приложений](https://12factor.net/ru/)
- [NGINX Blog: Introduction to microservices](https://www.nginx.com/blog/introduction-to-microservices/)

- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Отчуждаемый от IDE линтер](https://github.com/hadolint/hadolint)
- [Test framework для Dockerfile](https://opensource.googleblog.com/2018/01/container-structure-tests-unit-tests.html)

- [Troubleshooting docker-machine](https://docs.docker.com/toolbox/faqs/troubleshoot/)


Создадим специальную сеть для приложения:
```
docker network create reddit
```

```
Адреса для взаимодействия контейнеров задаются через ENV-переменные внутри Dockerfile'ов
```
Создадим Docker volume:
```
docker volume create reddit_db
```
Запустим наши контейнеры:
```
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db -v reddit_db:/data/db mongo:latest
docker run -d --network=reddit --network-alias=post feizus/post:1.0
docker run -d --network=reddit --network-alias=comment feizus/comment:1.0
docker run -d --network=reddit -p 9292:9292 feizus/ui:2.0
```
```
:2.0 - Версия конейнера
```
```
-v reddit_db:/data/db - volume
```
Выключение запущенных контейнеров:
```
docker kill $(docker ps -q)
```


# Homework 17 Docker-4

- [goss - утилита для тестирования инфраструктуры](https://github.com/aelsabbahy/goss)
- [dgoss - обертка на bash](https://github.com/aelsabbahy/goss/tree/master/extras/dgoss)
- [Container-structure-test от Google](https://github.com/GoogleContainerTools/container-structure-test)

