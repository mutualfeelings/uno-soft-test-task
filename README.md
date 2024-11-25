# Описание, выполненной работы:

Конфигурация из 3-ёх узлов Cassandra, каждый из них в кластере будет доступен через порт 9042 и будет знать о других узлах в качестве сидов. Каждый узел доступен по своему IP-адресу в локальной сети.

```shell

```

1. В 'services' определяем сервисы (контейнеры) для развертывания. cassandra-1, cassandra-2, cassandra-3 - имена сервисов, каждый из которых соответствует одному из узлов кластера Cassandra.
2. 'image: cassandra:latest' определяет образ Docker, который будет использоваться для создания контейнеров Cassandra. В данном случае последняя доступная версия
3. 'container_name' определяет имена контейнеров
4. 'ports: - "9042:9042"' порт 9042, используемый Cassandra для внешнего доступа, пробрасывается с хостовой машины на каждый контейнер.
5. 'environment' и 'env_file' устанавливают переменное окружение 'CASSANDRA_SEEDS', CASSANDRA_SEEDS=192.168.1.200 Указывает контейнер cassandra-1 в качестве сида для связи и обмена данными между узлами кластера.
6. Сеть, к которой принадлежат контейнеры 'networks: - cassandra_macvlan'. Каждый контейнер Cassandra автоматически присоединяется к общей сети секции networks. Docker назначает IP-адреса для каждого контейнера в этой сети автоматически.
7. 'expose' определяет порты, которые будут открыты внутри контейнера.

Успешное подключение к узлу cassandra1, он доступен по локальному id через CQL (язык запросов Cassandra)
> ![image]()

# Развертывание кластера Cassandra с помощью Docker Compose

Этот проект демонстрирует настройку кластера Cassandra из трех узлов с использованием Docker Compose. 

## Системные требования

•	Docker
•	Docker Compose
•	Cassandra Tools для подключения


## Инструкция

### Шаг 1. Создание виртуальных машин 'cassandra-server' и 'cassandra-client' 
```shell
vagrant up
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

# Установка cassandra-tools для подключение к кластеру 
'sudo snap install cqlsh'

# Подключение к первому узлу
'cqlsh 192.168.1.200 9042'

# Подключение ко второму узлу
'cqlsh 192.168.1.201 9042'

# Подключение к третьему узлу
'cqlsh 192.168.1.202 9042'

