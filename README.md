# Установка и настройка Sonaype Nexus для храннеия артефактов

## Установка Sonatype Nexus
- Установить Docker.
- Создать папку для хранения данных Nexus и назначить права:
```SSH Config
# mkdir -p /docker_volumes/nexus-data && chown -R 200 /docker_volumes/nexus-data
```
- Скопировать на целевую машину docker-compose.yaml из этого проекта.
- Запустить docker с использование compose файла:
```SSH Config
# docker compose up -d
```

## Настройка Sonatype Nexus
- После запуска Sonatype Nexus зайти на целевую машину по http, сменить пароль пользователя admin (на страничке будет сказано как).


## Создание Docker registry
- Создать новый репозиторий требуемого типа (Server administration and configuration - Repositories - Create Repository).
Тип: docker-hosted.
  - Для репозитория указать http порт (repository connector), который настроен вторым (и далее) портом в docker-compose.yaml. В нашем случае - 5000.
- Для разграничения доступа к репозиторию требуется создать отдельного пользователя с ограниченными правами:
  - зайти в Security - Role - Create role, указать тип роли "Nexus role";
  - заполнить Role ID и Role Name, например nx-upload;
  - добавить привилегий через "Modify Applied Priviliges":
    - nx-component-upload;
    - nx-repository-view-docker-имя_репо-*;
  - создать пользователя для доступа к репо Security - Users - Create local user;
  - заполнить все поля и присвоить пользователю ранее созданную роль nx-upload.

## Проверка
- На машине с которой планируется работать с репо создать файл /etc/docker/daemon.json и добавить в него содержимое:
```YAML
{
        "insecure-registries" : ["имя_сервера_nexus:5000"]
}

```
- Выполнить логин в репо: 
```
# docker login имя_сервера_nexus:5000
```
- Присвоить тег существующему образу:
```
# docker tag id_образа имя_сервера_nexus:5000/тег
```
- Выполнить пуш в репо:
```
# docker push имя_сервера_nexus:5000/тег
```