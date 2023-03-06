# Домашнее задание к занятию "13.1 контейнеры, поды, deployment, statefulset, services, endpoints"
Настроив кластер, подготовьте приложение к запуску в нём. Приложение стандартное: бэкенд, фронтенд, база данных. Его можно найти в папке 13-kubernetes-config.

## Задание 1: подготовить тестовый конфиг для запуска приложения

### Вопрос
Для начала следует подготовить запуск приложения в stage окружении с простыми настройками. Требования:
* под содержит в себе 2 контейнера — фронтенд, бекенд;
* регулируется с помощью deployment фронтенд и бекенд;
* база данных — через statefulset.

### Ответ

На основе 13-kubernetes-config создаем образы [frontend](https://hub.docker.com/r/rdbmw/front-app) и [backend](https://hub.docker.com/r/rdbmw/back-app) загружаем их на hub.docker.com для дальнейшего использования в манифестах.

Далее, создаем манифесты:
- [stage_ns.yml](src/stage/stage_ns.yml) для создания Namespace stage.
- [statefulset.yml](src/stage/statefulset.yml) для создания StatefulSet на основе образа с БД Postgres.
- [service_db.yml](src/stage/service_db.yml) - Service для возможности доступа бэкенда к БД. 
- [deployment.yml](src/stage/deployment.yml) - описываем Deployment с одним подом и двумя контейнерами frontend и backend.

Применяем манифесты через ```kubectl apply -f``` и проверяем, что все ресурсы созданы и работают. 

![Скриншот](img/Task1_1.png)

Проверим, что бэкенд успешно подключился к БД и сгенерировал демо-данные.

![Скриншот](img/Task1_2.png)

Проверим, что из контейнера фронтенда доступно api бекенда

![Скриншот](img/Task1_3.png)

Сделаем port forward для проверки ответа фронтенда

![Скриншот](img/Task1_4.png)

Фронтенд отвечает, но есть проблема с CORS (не стал пока разбираться как решить)


## Задание 2: подготовить конфиг для production окружения

### Вопрос
Следующим шагом будет запуск приложения в production окружении. Требования сложнее:
* каждый компонент (база, бекенд, фронтенд) запускаются в своем поде, регулируются отдельными deployment’ами;
* для связи используются service (у каждого компонента свой);
* в окружении фронта прописан адрес сервиса бекенда;
* в окружении бекенда прописан адрес сервиса базы данных.

### Ответ

Используем те же образы [frontend](https://hub.docker.com/r/rdbmw/front-app) и [backend](https://hub.docker.com/r/rdbmw/back-app) и описываем ресурсы:
- [prod_ns.yml](src/prod/prod_ns.yml) для создания Namespace prod.
- [statefulset.yml](src/prod/statefulset.yml) для создания StatefulSet на основе образа с БД Postgres.
- [service_db.yml](src/prod/service_db.yml) - Service для возможности доступа бэкенда к БД. 
- [deployment_back.yml](src/prod/deployment_back.yml) - описываем Deployment для бэкенда.
- [service_back.yml](src/prod/service_back.yml) - Service для возможности доступа фронтенда к бэкенду.
- [deployment_front.yml](src/prod/deployment_front.yml) - описываем Deployment для фронтенда.

Применяем манифесты через ```kubectl apply -f``` и проверяем, что все ресурсы созданы и работают.

![Скриншот](img/Task2_1.png)

Проверим, что бэкенд успешно подключился к БД и сгенерировал демо-данные.

![Скриншот](img/Task2_2.png)

Проверим, что из фронтенду доступно api бекенда

![Скриншот](img/Task2_3.png)

Проверим, что фронтенд отвечает

![Скриншот](img/Task2_4.png)