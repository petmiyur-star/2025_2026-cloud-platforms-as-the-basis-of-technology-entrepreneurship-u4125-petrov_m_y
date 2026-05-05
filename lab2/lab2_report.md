University: ITMO University (https://itmo.ru/ru/)
Faculty: FICT (https://fict.itmo.ru)
Course: Cloud platforms as the basis of technology entrepreneurship
Year: 2025/2026
Group: U4125
Author: Petrov Mikhail Yurievich
Lab: Lab2
Date of create: 05.05.2026
Date of finished: 05.05.2026
# Лабораторная работа №2: Исследование Cloud Run

## Цель работы
Ознакомиться с работой сервиса Google Cloud Run, научиться развёртывать контейнерные приложения, анализировать логи и метрики, управлять трафиком между ревизиями.
## Ход выполнения

### 1. Создание сервиса из собственного образа
Был собран Docker-образ на основе Python + Flask. 
Код приложения `app.py`:

from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello World from custom Cloud Run app!'

if __name__ == '__main__':
    import os
    port = int(os.environ.get('PORT', 8080))
    app.run(host='0.0.0.0', port=port)

Образ собран и загружен в Artifact Registry:
gcloud builds submit --tag gcr.io/cloud-platforms-as-the-basis/petrov-custom-hello

Развернут сервис `petrov-custom` в регионе `europe-west1` с публичным доступом, порт 8080:
gcloud run deploy petrov-custom \
  --image gcr.io/cloud-platforms-as-the-basis/petrov-custom-hello \
  --platform managed --region europe-west1 --allow-unauthenticated --port 8080
**Скриншот 1:** страница сервиса в консоли с URL.
**Скриншот 2:** открытый в браузере URL, видно приветствие.
### 2. Анализ логов (Cloud Logging)
Через **Logs Explorer** выполнен запрос по ресурсу `cloud_run_revision` и сервису `petrov-custom`.
Логи содержат записи о запуске контейнера, обработке HTTP-запросов (метод GET, статус 200).
**Скриншот 3:** интерфейс Logs Explorer с результатами запроса.
### 3. Анализ метрик (Cloud Monitoring)
Через **Metrics Explorer** выбран ресурс `Cloud Run Revision` и метрика `Request count`. Построен график запросов к сервису.
**Скриншот 4:** график Request count.
### 4. Изменение порта контейнера и создание новой ревизии
На странице сервиса нажата кнопка **Edit & Deploy new revision**. Порт изменён с `8080` на `8090`.
Новая ревизия `petrov-custom-00002` успешно запустилась (образ читает переменную PORT).
**Скриншот 5:** список ревизий, где обе версии в статусе Ready.
### 6. Очистка ресурсов
Сервис удалён командой:
gcloud run services delete petrov-custom --region europe-west1 --quiet
## Выводы
- Cloud Run – полностью управляемая serverless платформа для запуска контейнеров.
- Приложение должно слушать порт, переданный в переменной окружения PORT.
- Логи и метрики встроены в экосистему Google Cloud.
- Cloud Run поддерживает несколько ревизий и гибкое управление трафиком.
- Масштабирование от нуля позволяет экономить ресурсы.
## Полезные ссылки
- [Cloud Run quickstart](https://cloud.google.com/run/docs/quickstarts)
- [Logging and Monitoring for Cloud Run](https://cloud.google.com/run/docs/logging)
- [Managing traffic between revisions](https://cloud.google.com/run/docs/rolling-back)
  
