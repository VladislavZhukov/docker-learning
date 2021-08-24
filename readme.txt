// показывает все опции
docker
//узнать команды
// docker --help
// docker ps --help
// docker run --help

// images: отобразит все images
docker images

// ps: посмотреть запущенные контейнеры
// -a: посмотреть все контейнеры
// -q: только ID
docker ps
docker ps -a
docker ps -a -q

// pull: скачивает образ images, не делает из него контейнер
// также можно скачать определенную версию
docker pull grafana/grafana
docker pull grafana/grafana:6.2.5

// run: команда сделает контейнер
// -p: порт
// -d: режим detached (автономный режим)
// -it: подробно видеть код запуска
docker run -d --name=grafana -p 3000:3000 grafana/grafana
docker run -it --name=grafana -p 3000:3000 grafana/grafana
docker run -it python-app:1.0 "echo" "override command"
docker run -it --name app1 -p 5001:5001 python-app:2.0
docker run -it --name app2 -p 5002:5002 -e "port=5002" python-app:2.0

//create: создает контейнер но не зпускает
docker create --name=grafana -p 3000:3000 grafana/grafana

//rm: удалит контейнер
//rmi: удалит images(образ)
docker rm -f grafana
docker rm -f 9cbcdf17cdd1
docker rm -f grafana app-stopped grafana2
docker rmi selaworkshops/npm-static-app
// удалит все контейнеры
docker container prune

//присоеденяемся к процессу ввода/вывода серверному
docker attach app
ctrl + c (выход)

//вход в альтернативный терминал (вход в контейнер)
docker exec -it grafana /bin/bash
// посмотреть все файлы в контейнере не оставаясь в нем
docker exec gapp2 ls -l

//создает новый образ из измененного контейнера
docker commit gapp selaworkshops/npm-static-app:3.1

// сохранить images образ в локальный файл на PC
docker save -o static-app-3.1.tar selaworkshops/npm-static-app:3.1
// загрузить images образ с локального PC
docker load -i static-app-3.1.tar

//build из файла Dockerfile (Dockerfile не имеет разширения)
docker build -t python-app:1.0 .
docker build --build-arg port=5001 -t python-app:2.0 .

//Dockerfile
//FROM устанавливаем базовые images
FROM ubuntu:20.04
//ARG оперделяет используемые переменные во время сборки
ARG BASE_IMAGE=ubuntu:20.04
FROM ${BASE_IMAGE}
// ENV используется для назначения среды
ENV MY_NAME="JOJO"
ENV ASPNETCORE_ENVIRONMENT=Development
// RUN построение images по шагам
RUN apt-get update
RUN apt-get-install -y apt-utils
//WORKDIR используется для установки рабочего коталога внутри контейнера
WORKSDIR /etc/nginx
//COPY копирует файлы из src и добавляет в файловую систему контейнера
COPY nginx.conf /etc/nginx/nginx.conf
//ADD используется аналогично COPY, может задать url адрес, разпоковывает архив на файлы
ADD mysite.tar.gz /etc/nginx/sites-available/
// EXPOSE уазывает на сетевой порт
EXPOSE 80
// ENTRYPOINT идет перед CMD, настраивает вспомогательные фоновые процессы (протоколирование), подключение БД, настройки сервера
ENTRYPOINT ["ngenix"]
//CMD выполняется каждый раз когда контейнер запущенные, когда команда завершается контейнер закрывается
CMD ["nginx","-g","demon off"]

//stop: останавливает работу контейнера (мягко)
docker stop 744ed3204711
//kill: останавливает работу контейнера принудительно (грубо)
docker kill 744ed3204711

//start: запускает контейнер
docker start 744ed3204711
//restart: перезапуск контейнера
docker restart 744ed3204711

//info: отображает системную информацию
docker info
//stats: отображает в реальном времени поток используемых ресурсов
docker stats

//top: отображает запущенные процессы контейнера
docker top 744ed3204711

//cp: копирует файлы / папки между контейнерами в локальную файлоаую систему
docker cp app2:/app/index.html .

//history: показыапет историю images (обращаться к образу)
docker history python-app:2.0

//inspect: возвращает информацию об объектаз docker (обращаться к образу)
docker inspect python-app:2.0

//logs: выводит стандартный поток ошибок и выводов работающего контейнера
docker logs --follow 744ed3204711

//diff: проверяет изменение файлоа или каталогов в файловой системе контейнера
docker diff

// volume (тома)
// когда контейнер умирает, все данные, которые он создал (журналы, базы данных и т.д.)
// умирают вместе с ним.
// Volume(тома) - внешнее хранилище
// области, ипользуемые для хранилище данных производятся контейнером Docker.
// тома могут быть расположенны на host docker или даже на удаленной машине.
// по умолчанию тома не удаляются при остановке контейненра
// Объемы данных могут быть разделенны между контейнерами
// Тома можно монтировать в режиме только для чтения
--volume: создать файл или каталог если он не находится в docker host
--mount: не создает его автоматически, но генериркет ошибку
// volume create: создать том 
docker volume create volume-name
// volume ls: список всех томов
docker volume ls
// volume inspect: посмотреть том
docker volume inspect volume-name
// volume rm: удалить том
docker volume rm volume-name
// volume prune: удалить все тома
docker volume prune
// example
docker run -it -v my_volume:/data --name my-container selaworkshops/busybox:latest 

// Networks
// Docker включает потдержку сети контейнеров через использоване сетевых драйверов
// По умолчанию контейненру назначается ip-адрес для каждой сети docker, к которой он подключен 
// ( демон docker действует как DHCP сервер )
// По умолчанию контейнер наследует настройки DNS Docker. (можно переопределить для каждого контейнера)
// Network Drivers
// bridge (мост): позволяет контейненрам, подключенным к одной сети моста, общаться, обеспечивая изоляцию от контейнеров,
// которые не подключены к этой сети моста
// host (хост): для автономных контейнеров удалит изоляцию сети, между контейнером и хостом, и дает возможность
// напрямую использовать сеть хоста.
// overlay (наполнение): создает распределенную сеть между несколькими хостами Docker.
// macvlan: сети Macvlan позволяют назначать MAC-адрес в контейнер, чтобы он отображался как физическое устройство на вашей сети.
// none: отключить всю сеть контейнера.
// Network Plugins: можно установить и использовать стороннюю сеть плагинов Docker.
// Default Networks: при установке Docker Engine автоматически включатеся три сети, также docker всегда запускает контейнеры в bridge сеть (если не изменить)
// network create: создать сеть
// docker network create -d bridge network-name
// network rm: удалить сеть
// docker network rm network-name
// запуск контейнера с определенной сетью
// docker run [OPTIONS] --network=network-name [IMAGES]
// docker run -d -p 8081:8081 -e "prot=8081" --name my_app --network=network-name selaworkshops/python-app:2.0
// docker run -it --name my_app2 selaworkshops/ubuntu:18.04
// network connect: добавить работающий контейнер в сеть
// docker network connect network-name [CONTAINER ID]
// docker network connect my-bridge-network ubuntu
// network disconnect: отключить контейнер от сети
// docker network disconnect network-name [CONTAINER ID]
// network inspect: посмотреть сеть
// docker network inspect network-name
// network prune: удалить все не используемые сети
// docker network prune
// curl: команда для подкючения к сети
// находясь внутри сети /# curl 172.18.0.2:8081

// Docker Registries
// Используется для обменами оброзами Docker и управления ими
// Docker hub - это реестр по умолчанию
// docker build .
// docker tag eaf gilleks/my-repository:my-image
// или
// docker build -t gilleks/my-repository:latest .
// docker login -u gilleks -p myPassword

# // внутри терминала
# // посмотреть все файлы
# ls -l
# // посмотреть где мы находимся
# pwd
# создать файл с название secret-file и записать в него JOJO was here
# echo "JOJO was here" > secret-file
# зайти в файл и увидеть содержимое
# cat secret-file
# // выход
# exit

# // windows cmd command
# //mkdir: создать папку
# mkdir test-folder
# //cd: переход по папкам
# cd test-folder