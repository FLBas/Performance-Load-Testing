# Нагрузочное тестирование WebTours (JMeter, Grafana, InfluxDB, Telegraf)

Проект посвящен проведению нагрузочного тестирования веб-приложения WebTours для определения максимальной (Lmax) и пиковой (Lpeak) производительности.
## 🛠 Стек технологий
* **Генератор нагрузки:** Apache JMeter
* **Сбор и хранение метрик:** InfluxDB, Telegraf
* **Визуализация:** Grafana

## 🎯 Цели и требования (SLA)
* Время отклика (90-й перцентиль): не более 3 сек.
* Уровень ошибок: не более 5% (общий и по сценариям).
* Утилизация CPU: не более 80%.
* Успешное прохождение нагрузки в 150% от базового профиля.

## 📊 Результаты тестирования

### 1. Поиск максимальной производительности (Lmax)
Ступень максимальной производительности зафиксирована на уровне **136 потоков (11-я ступень, 850% от профиля)**.
* **Пропускная способность:** 8460 TPH
* **Max время отклика:** 1.81 мс
* **Утилизация CPU:** 9.25%
* **Процент ошибок:** 0.06%
* **Вывод:** Система выдержала нагрузку, требования SLA не нарушены.

> [!NOTE]
>  Transactions Response Times (90th pct) without Transaction Controller and Error requests
> <img width="1734" height="414" alt="image" src="https://github.com/user-attachments/assets/c95a79ff-4320-4baf-886c-7deee1c72e6b" />
>  Throughput per minutes 
> <img width="1751" height="383" alt="image" src="https://github.com/user-attachments/assets/930828ea-0f90-45e5-9e51-b47b99b03fd5" />
>  Errors by UC_Main (per minutes) 
> <img width="1771" height="391" alt="image" src="https://github.com/user-attachments/assets/00eff779-97f8-493e-b306-26feef3ed1fe" />




### 2. Поиск пиковой производительности (Lpeak)
Пик зафиксирован на уровне **208 потоков (20-я ступень, 1300% от профиля)**.
* Прирост пропускной способности составил менее требуемых 5% (30 TPH).
* Время отклика деградировало до 17.8 секунд.

> [!NOTE]
> Throughput per minutes
> <img width="1928" height="372" alt="image" src="https://github.com/user-attachments/assets/1dc178b1-8aec-41bf-ac0a-be524b626619" />


### 3. Подтверждение максимума
Часовой тест на подтвержденном уровне Lmax (136 потоков) показал стабильную работу приложения: пропускная способность составила 8435 TPH (отклонение 0.76%), ошибки — 0.11%.

> [!NOTE]
> Transactions Response Times (90th pct) without Transaction Controller and Error requests
> <img width="1524" height="315" alt="image" src="https://github.com/user-attachments/assets/f7e26cd6-7c2f-4c9f-9b06-da8c37565b44" />
> Throughput per minute
> <img width="1551" height="335" alt="image" src="https://github.com/user-attachments/assets/14d954fc-3310-4ebf-a17a-12aef2a3463a" />

## 🐞 Выявленные дефекты
**JIRA-1: Premature end of script headers**
При повышении нагрузки до 96–104 потоков (600–650% профиля) веб-сервер не справляется с очередью запросов и массово прерывает выполнение скриптов (`nav.pl`, `login.pl`, `welcome.pl`, `reservation.pl`) до возврата HTTP-заголовков клиенту.

## 💡 Рекомендации
* Провести глубокий анализ обработки CGI скриптов приложением.
* Рассмотреть переход архитектуры на **FastCGI** для предотвращения обрывов пользовательских сессий при авторизации и навигации под высокой нагрузкой.
