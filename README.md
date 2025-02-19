# описание структуры docker compose файла
version: "3.5"
services:
  app_apache:
    #если уже сущетсвует докерфайл с собственным контейнером, то вместо указания образа image: <image>, можно указать build: . | важно, чтобы докерфайл находился в той же директории, что и докер-компоуз файл
    image: php:apache
    container_name: test-apache
    ports:
      - '80:80'
      - '443:443'
    #unless-stoped указывает на то, что приложение будет иметь состояние такое же как и до выключения или перезапуска хоста, на котором работает контейнер
    restart: unless-stoped
    #depend_on указывает на то, что приложение, в данном случае apache, должен запускаться после того, как запуститься БД и Редис, чтобы приложение не выдавало ошибку о зависимостях и отсутствие работы БД
    depends_on:
      - app_db
      - app_redis
    #данные сети служат для логического раздиления сетей, для самого веб сервера будет использоваться сеть internet с драйвером bridge, для бэка appnet, чтобы контейнеры могли взаимодействовать между собой
    networks:
      - internet
      - appnet

  app_db:
    image: postgres
    container_name: db-postgress
    restart: unless-stoped
    enviroment:
      - 'POSTGRES_PASSWORD=admin'
    networks:
      - appnet

  app_redis:
    image: redis
    container_name: app-redis
    restart: unless-stoped
    networks:
      - appnet
#описание сетевых карт для контейнеров
networks:
  internet:
    name: internet
    driver: bridge
  appnet:
    name: appnet
    driver: bridge
