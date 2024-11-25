# Описание, выполненной работы:

Конфигурация из 3-ёх узлов Cassandra, каждый из них в кластере будет доступен через порт 9042 и будет знать о других узлах в качестве сидов. Каждый узел доступен по своему IP-адресу в локальной сети.

```shell
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS     NAMES
1bb19714a5da   cassandra:latest   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes             cassandra-1
5518052233f1   cassandra:latest   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes             cassandra-2
148a49537151   cassandra:latest   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes             cassandra-3
```

1. В 'services' определяем сервисы (контейнеры) для развертывания. cassandra-1, cassandra-2, cassandra-3 - имена сервисов, каждый из которых соответствует одному из узлов кластера Cassandra.
2. 'image: cassandra:latest' определяет образ Docker, который будет использоваться для создания контейнеров Cassandra. В данном случае последняя доступная версия
3. 'container_name' определяет имена контейнеров
4. 'ports: - "9042:9042"' порт 9042, используемый Cassandra для внешнего доступа, пробрасывается с хостовой машины на каждый контейнер.
5. 'environment' и 'env_file' устанавливают переменное окружение 'CASSANDRA_SEEDS', в 'env_file' вынесены общие переменные, CASSANDRA_SEEDS=192.168.1.200 Указывает контейнер cassandra-1 в качестве сида для связи и обмена данными между узлами кластера.
6. Сеть, к которой принадлежат контейнеры 'networks: - cassandra_macvlan'. Каждый контейнер Cassandra автоматически присоединяется к общей сети секции networks. Docker назначает IP-адреса для каждого контейнера в этой сети автоматически.
7. 'expose' определяет порты, которые будут открыты внутри контейнера.

Успешное подключение к узлу cassandra1, он доступен по локальному id через CQL (язык запросов Cassandra)
> ![image](https://raw.githubusercontent.com/mutualfeelings/uno-soft-test-task/0a73cd2696cc41e3ada46793152ae5e343d52e65/image.png)

# Развертывание кластера Cassandra с помощью Docker Compose

Этот проект демонстрирует настройку кластера Cassandra из трех узлов с использованием Docker Compose. 

## Системные требования

•	Docker
•	Docker Compose
•	Cassandra Tools для подключения


## Инструкция

### Шаг 1. Создание macvlan-сеть на машине A
```shell
docker network create \
  --driver=macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=ens256 \
  cassandra_macvlan
```

### Шаг 2. Настройка сети

Создание дополнительного интерфейса macvlan на сервере.
```shell
ip link add macvlan0 link ens256 type macvlan mode bridge
ip addr add 192.168.1.199/24 dev macvlan0
ip link set macvlan0 up
```

Включение маршрутизации между хостом и контейнерами. 
```shell
ip route add 192.168.1.200/30 dev macvlan0
```

Добавление SNAT (Source NAT) на сервере А (192.168.1.197) для перенаправления трафика между ens256 и macvlan0. 
```shell
sudo iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o macvlan0 -j MASQUERADE
```

Добавление разрешеня для трафика
```shell
sudo iptables -A FORWARD -s 192.168.1.0/24 -d 192.168.1.0/24 -j ACCEPT
```

Настройка маршрута на сервере Б (192.168.1.198). 
```shell
sudo ip route add 192.168.1.200/30 via 192.168.1.197 dev ens256
```

### Шаг 2. Запуск контейнеров
```shell
sudo docker compose up -d
```
Проверить, что все контейнеры работаю:
```shell
sudo docker ps -a 
```

### Шаг 3. Проверка подключения с машины Б

Установка cassandra-tools для подключение к кластеру 
```shell
sudo snap install cqlsh
```

Подключение к первому узлу
```shell
cqlsh 192.168.1.200
```

Подключение ко второму узлу
```shell
cqlsh 192.168.1.201
```

Подключение к третьему узлу
```shell
cqlsh 192.168.1.202
```
