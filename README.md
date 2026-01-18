<!-- Copyright (c) Meta Platforms, Inc. and affiliates.
License found in the LICENSE file in the root directory
of this source tree. -->
# Прокси для WhatsApp

[<img alt="github" src="https://img.shields.io/badge/github-WhatsApp/proxy-8da0cb?style=for-the-badge&labelColor=555555&logo=github" height="20">](https://github.com/WhatsApp/proxy)
[![CI](https://github.com/WhatsApp/proxy/actions/workflows/ci.yml/badge.svg)](https://github.com/WhatsApp/proxy/actions/workflows/ci.yml)

Если вы не можете подключиться напрямую к WhatsApp, можно использовать прокси в качестве шлюза между вами и нашими серверами. Чтобы помочь себе или другим восстановить соединение с WhatsApp, вы можете настроить собственный прокси-сервер.

Если у вас уже есть готовый прокси, вы можете подключить его к WhatsApp, следуя инструкциям в этой [статье](https://faq.whatsapp.com/520504143274092).

## Часто задаваемые вопросы

**ОБЯЗАТЕЛЬНО ПРОЧИТАЙТЕ ПЕРЕД СОЗДАНИЕМ ISSUE**  
У нас есть FAQ, который можно найти здесь: [FAQ.md](https://github.com/whatsapp/proxy/blob/main/FAQ.md)

## Что вам понадобится

1. [Docker](https://docs.docker.com/engine/install/) (включите автозапуск Docker, если ваша система это позволяет)
2. [Docker Compose](https://docs.docker.com/compose/) (опционально)

## Настройка вашего прокси

**ОБНОВЛЕНИЕ** Теперь существует готовый образ, размещённый в репозитории DockerHub от Meta. Вам больше не нужно собирать образ самостоятельно (если, конечно, вы не хотите его кастомизировать).

```bash
docker pull idsmef/whatsapp_proxy:latest
После этого вы можете пропустить раздел Сборка контейнера и перейти сразу к Запуск прокси, заменив все упоминания тега whatsapp_proxy:1.0 на idsmef/whatsapp_proxy:latest.
1. Клонируйте репозиторий на свой компьютер
Bashgit clone https://github.com/WhatsApp/proxy.git
В текущей директории должна появиться папка proxy.
2. Установите Docker для вашей системы
Чтобы убедиться, что Docker успешно установлен:
Bashdocker --version
должна отобразиться строка вида Docker version 20.10.21, build baeda1f.
2. (Опционально) Установите Docker Compose
Для пользователей Linux, если ваша версия Docker не включает Docker Compose, можно установить его отдельно:
Bash# Скачиваем пакет
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$$   (uname -s)-   $$(uname -m) -o /usr/bin/docker-compose
# Делаем исполняемым
sudo chmod +x /usr/bin/docker-compose
3. Сборка контейнера прокси (если не используете готовый образ)
Перейдите в директорию репозитория:
Bashcd proxy
Соберите контейнер командой:
Bashdocker build proxy/ -t whatsapp_proxy:1.0
Вы должны увидеть сообщение примерно такого вида: [+] Building 6.6s (18/18) FINISHED. Контейнер будет скомпилирован и помечен тегом whatsapp_proxy:1.0.
Запуск прокси
Запуск контейнера вручную
Вы можете запустить Docker-контейнер следующей командой:
Bashdocker run -it -p 80:80 -p 443:443 -p 5222:5222 -p 8080:8080 -p 8443:8443 -p 8222:8222 -p 8199:8199 -p 587:587 -p 7777:7777 whatsapp_proxy:1.0
Вы увидите строки, заканчивающиеся на Certificate generation completed.. HAProxy будет работать в фоновом режиме, пока процесс не будет закрыт.
Проверка подключения
Чтобы убедиться, что HAProxy работает, откройте в браузере:
http://<ваш-публичный-ip>:8199
(где <ваш-публичный-ip> — это внешний IP-адрес вашего сервера).
По этой же ссылке можно отслеживать статистику прокси.
ПРИМЕЧАНИЕ: Если ваш публичный IP-адрес недоступен извне, вам нужно настроить проброс портов (port forwarding) на роутере/шлюзе для всех перечисленных выше портов. Поскольку настройка зависит от конкретной модели роутера, мы не будем подробно описывать этот процесс.
Если вам нужен формат OpenMetrics, используйте:
http://<ваш-публичный-ip>:8199/metrics
Разное
Обзор архитектуры прокси WhatsApp
В зависимости от сценария использования, контейнер прокси открывает несколько портов. Основные порты:

80   — обычный веб-трафик (HTTP)
443  — зашифрованный веб-трафик (HTTPS)
5222 — трафик протокола Jabber (стандартный порт WhatsApp)
587 или 7777 — трафик *.whatsapp.net, включая медиа (HTTPS)

Также настроены порты, которые принимают входящие соединения с заголовками PROXY protocol (версии 1 или 2). Это полезно, если перед прокси стоит сетевой балансировщик нагрузки и вы хотите сохранять оригинальный IP клиента.

8080 — HTTP с ожиданием PROXY protocol
8443 — HTTPS с ожиданием PROXY protocol
8222 — Jabber с ожиданием PROXY protocol

Генерация сертификатов для SSL-портов
Порты 443 и 8443 защищены самоподписанным сертификатом, который генерируется при старте контейнера. Можно настроить дополнительные параметры сертификата:

SSL_DNS — список альтернативных имён хостов через запятую (по умолчанию пусто)
SSL_IP — список альтернативных IP-адресов через запятую (по умолчанию пусто)

Пример задания при сборке:
Bashdocker build . --build-arg SSL_DNS=test.example.com
Продвинутые настройки
Автоматизация с помощью Docker Compose
Docker Compose позволяет удобно управлять контейнерами, автоматически применять нужные параметры и перезапускать их при сбоях. Мы рекомендуем использовать Docker Compose вместо ручного запуска (кроме тестов).
В репозитории есть пример файла docker-compose.yml.
Запуск в интерактивном режиме для проверки:
Bashdocker compose -f /path/to/this/repo/docker-compose.yml up
Запуск в фоновом режиме (как сервис):
Bashdocker compose -f /path/to/this/repo/docker-compose.yml up -d
Остановка:
Bashdocker compose down
Автозапуск при перезагрузке сервера
После настройки Docker Compose можно добавить автозапуск через systemd (если ваша система его поддерживает).
В репозитории есть пример файла docker_boot.service, который нужно адаптировать под вашу среду.
Установка systemd-сервиса:
Bash# Копируем файл в нужную папку
cp -v docker_boot.service /etc/systemd/system/
# Включаем автозапуск при загрузке
systemctl enable docker_boot.service
# Запускаем сервис
systemctl start docker_boot.service
# Проверяем статус контейнеров
docker ps
Не забудьте указать правильный путь к вашему файлу docker-compose.yml в файле сервиса.
Развёртывание в Kubernetes
Если вы хотите использовать Kubernetes, смотрите наш Helm chart README.
Другие варианты деплоя описаны здесь: /docs/deployments.md.
Контрибьюторы
Автором кода является Sean Lawlor .
Подробнее о том, как внести вклад в проект: CONTRIBUTING.md.
Лицензия
Проект распространяется по лицензии MIT.
