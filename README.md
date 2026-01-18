<!-- Copyright (c) Meta Platforms, Inc. and affiliates.

License found in the LICENSE file in the root directory
of this source tree. -->
<!-- Copyright (c) Meta Platforms, Inc. and affiliates. Лицензия находится в файле LICENSE в корневой директории этого исходного кода. -->
Прокси для чатов WhatsApp

<img alt="github" src="https://img.shields.io/badge/github-WhatsApp/proxy-8da0cb?style=for-the-badge&labelColor=555555&logo=github" height="20">



Если вы не можете подключиться к WhatsApp напрямую, можно использовать прокси как шлюз между вами и серверами WhatsApp. Чтобы восстановить подключение для себя или других, можно настроить прокси-сервер.

Если у вас уже есть прокси, его можно подключить к WhatsApp, следуя шагам в этой статье
.

Часто задаваемые вопросы

ПОЖАЛУЙСТА, ПРОЧИТАЙТЕ ЭТО ПЕРЕД СОЗДАНИЕМ ИНЦИДЕНТА
У нас есть FAQ, который можно найти здесь: FAQ.md

Что потребуется

Docker
 (включите Docker при запуске, если система это позволяет)

Docker Compose
 (опционально)

Настройка прокси

ОБНОВЛЕНИЕ: Теперь доступен готовый образ в DockerHub от Meta. Сборка стандартного образа больше не требуется (если вы не хотите его кастомизировать).

```bash
docker pull idsmef/whatsapp_proxy:latest
```

После этого можно сразу перейти к разделу Запуск прокси, заменив любой тегf `whatsapp_proxy:1.0` with `ifsmef/whatsapp_proxy:latest`.

### 1. Клонируйте репозиторий на локальный компьютер

```bash
git clone https://github.com/vbiosrv/proxy.git
```

