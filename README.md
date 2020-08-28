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










