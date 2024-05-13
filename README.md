# Проектирование высоконагруженных сервисов
Курсовая работа в рамках 3-го семестра программы по Веб-разработке ОЦ VK x МГТУ им. Н.Э. Баумана (ex. "Технопарк") по дисциплине "Проектирование высоконагруженных сервисов"

#### Автор - [Ислам Османов](https://park.vk.company/profile/is.osmanov/ "Страница на портале VK x МГТУ")
#### Задание - [Методические указания](https://github.com/init/highload/blob/main/homework_architecture.md)

#### Содержание:
1. [Тема, функционал и аудитория](#1)
2. [Расчёт нагрузки](#2)
3. [Глобальная балансировка](#3)
4. [Локальная балансировка](#4)
5. [Логическая модель](#5)
6. [Физическая модель](#6)
7. [Алгоритмы](#7)
8. [Технологии](#8)
9. [Схема проекта](#9)
10. [Обеспечение надёжности](#10)
11. [Расчёт ресурсов](#11)

## Часть 1. Тема и целевая аудитория <a name="1"></a>

### Тема курсовой работы - **"Проектирование бизнес-мессенджера"**
В качестве примера и аналога выбран Slack.
Slack представляет собой бизнес-мессенджер, объединяющий людей с необходимой им информацией, превращая таким образом способ общения организаций. Основной особенностью Slack является поддержка тредов, что позволяет в рамках одного канала или чата создавать независимые ветки обсуждений, облегчая организацию и структурирование коммуникации. Кроме того, Slack интегрируется с большим количеством других служб, позволяя пользователям управлять проектами и задачами непосредственно из мессенджера. Являясь мощным средством коммуникации и сотрудничества, Slack легко адаптируется под потребности любой команды или организации.

### Ключевой функционал сервиса
- Регистрация и авторизация.
- Каналы: Открытые или закрытые каналы для различных проектов, отделов или тем, чтобы сохранять беседы организованными. Участники могут присоединяться, уходить или быть добавлены другими как требуется.
- Прямые сообщения (ПМ): Переписка с конкретным участником.
- Обмен файлами и хранение: Возможность делиться файлами, такими как изображения, документы, видео, электронные таблицы и т. д., непосредственно с компьютера.   
- Уведомления и Напоминания: Настройка персональных уведомлений на основе ключевых слов, упоминаний, каналов или прямых сообщений. Напоминания для установки дат окончания, повторов или других важных задач.
- Поиск: Находить прошедшие разговоры, файлы и информацию в каналах и прямых сообщениях.

### Ключевые продуктовые решения
- Треды: Cоздание отдельных веток обсуждений в рамках общего канала или чата. Они полезны для упорядочивания коммуникации и делают обсуждения более структурированными.
- Папки с каналами: Создание тематических папок с каналами.
- Реакции на сообщения в чатах/каналах.
- Бизнес боты.


### Целевая аудитория
- 65 млн пользователей по всему миру.[^1]
- 1.5 миллиарда сообщений в месяц.[^2]

## Часть 2. Расчёт нагрузки <a name="2"></a>

### Продуктовые метрики

- MAU - 65 млн пользователей [^1]
- DAU - 38.8 млн пользователей [^1]

### Средний размер харанилища на одного пользователя:
| Хранимые данные   | Размер на одного пользователя         |
|-------------------|------------------------------------------|
| Аватар              | `2 МБ`                            |
| Данные о пользователе             | `3 КБ`                     |

### Среднее количество действий пользователя в день

|Действие пользователя |Количество / день|
| ------------- |-------------|
|Регистрация|18.920 тыс|
|Авторизация|14.55 млн|
|Отправка сообщения|50 млн|
|Отправка файлов|10 млн|
|Просмотр сообщений|349.2 млн|
|Создание чатов|37.92 млн|
|Создание каналов|46.92 млн|
|Создание тредов|34.92 млн|
|Создание папок|34.92 млн|
|Реакции|14.92 млн|

- По статистике пользователь остаётся онлайн в среднем 9 часов. Исходя из предоставленных данных, мы знаем, что в системе в среднем авторизовано 9 часов в сутки и что в сутки активно 38.8 млн пользователей. Чтобы выяснить, сколько в среднем авторизуется пользователей в сутки, мы можем использовать эти данные. Если каждый пользователь в среднем проводит в системе 9 часов, то общее количество часов, проведенных всеми активными пользователями в сутки, составляет:
  ```
  38.8 млн * 9 часов = 349.2 млн часов.
  ```
  Так как в сутках 24 часа, то общее количество авторизаций пользователей в сутки можно вычислить, разделив общее количество часов, проведенных всеми активными пользователями, на количество часов в сутках.[^2]
  ```
  349.2 млн часов / 24 часа = 14.55 млн авторизаций в сутки.
  ```
- После пандемии потребность в онлайн платформах ведения бизнеса выросла и Slack не стал исключением. После 2020-го года наблюдается активный рост новых пользователей. На основе статистических данных можно получить среднюю дневную активность пользователей за 5 лет:
  ```
  ((47.2 - 38.8) + (38.8 - 32.3) + (32.3 - 25.7) + (25.7 - 19.2) + (19.2 - 12.6)) /  5 = 6.92 млн.
  ``` 
  Средняя дневная активность пользователей на сайте за 5 лет увеличилась на 6.92 млн пользователей в год. Это означает, что каждый день количество активных пользователей увеличивается примерно на:
   ```
   6.92 млн / 365 дней = 18920
   ```
  новых пользователей в день.[^1]
- В месяц отправляется 1.5 миллиарда сообщений из этого следует, что в день в среднем отправляется приблизительно [^2]
  ```
  1.5 млрд / 30 дней = 50 млн.
  ```
- Так как не удалось найти информации об отправке файлов за день предположим, что в день на 50 сообщений приходится 10 файлов. Тогда в день будет отправлено 10 млн файлов.
- В среднем человек проводит в соц. сетях 2 часа 25 минут, что составляет около 30 минут в час => таким образом пользователь заходит в соц сети 6-7 раз в день. Теперь, чтобы выяснить, сколько сообщений в среднем просматривает один пользователь в день, мы можем разделить общее количество сообщений в день (50 млн) на общее количество авторизаций в сутки (14.55 млн). Получим примерно 3.4 сообщения на одну авторизацию. Если учесть, что пользователь заходит в соцсети примерно 6-7 раз в день, то в среднем один пользователь просматривает около 20-24 сообщений в день.[^3]
  ```
  24 * 14.55 млн = 349.2 млн просмотренных сообщений в день.
  ```
- Так как не удалось найти информации о создании чатов/каналов/папок/тредов и реакций предположим, что на данные действия приходится 10% остальной активности т.к эти действия как правило выполняются один раз на конкретный кейс. Возьмём имеющийся у нас максимум по активности, а именно 349.2 млн => 349.2 млн * 0.1 = 34.92 млн. 

### Технические метрики

### Cредний размер хранилища на пользователя

|Хранилище | Стартовый размер (Пб) | Увеличение (Пб/год) |
| ------------- |-------------|-------------|
|Сообщения|0,052214|0,052214|
|Файлы|73|73|
|Данные пользователя|0,03084|0,03084|

#### С учётом выше представленных расчётов можно предположить приблизительные размеры хранилища. Предположим, что размер одного сообщения не больше 3Кб, тогда при условии что в день поступает 50 млн сообщений =>
- Чтобы вычислить годовой рост затрат на память, сначала необходимо определить ежедневный рост и умножить его на количество дней в году (365).
- Сначала найдем, сколько памяти используется каждый день для сообщений:
- Память, используемая для сообщений = Число сообщений × Средний размер сообщения
- Память, используемая для сообщений = 50 000 000 сообщений × 3 кБ/сообщение = 150 000 000 кБ (приблизительно)
- Затем давайте найдем, сколько памяти используется каждый день для файлов:
- Память, используемая для файлов = Число файлов × Средний размер файла
- Память, используемая для файлов = 10 000 000 файлов × 20 МБ/файл = 200 000 ГБ (приблизительно)
- Теперь переведем все значения в один и тот же единицу измерения (ГБ):
- Память, используемая для сообщений = 150 000 000 кБ / 1024 / 1024 = приблизительно 143.05 ГБ
- Память, используемая для файлов = 200 000 ГБ


#### Теперь найдём расход по памяти в год:
- Память, используемая для пользователей 6.92 млн * 2Мб = 30840000 МБ = 30,84 ТБ = 0,03084 ПБ
- Память, используемая для сообщений = 143.05 ГБ * 365 = 0,052214 ПБ
- Память, используемая для файлов = 200 000 ГБ * 365 = 73 ПБ



### Сетевой трафик и RPS

|Действие пользователя | RPD | RPS |
| ------------- |-------------|-------------|
|Регистрация|18.920 тыс|0.21|
|Авторизация|14.55 млн|168.4|
|Отправка сообщения|50 млн|578.7|
|Отправка файлов|10 млн|115.7|
|Просмотр сообщений|349.2 млн|4041.6|
|Создание чатов|34.92 млн|404.1|
|Создание каналов|34.92 млн|404.1|
|Создание тредов|34.92 млн|404.1|
|Создание папок|34.92 млн|404.1|
|Реакции|34.92 млн|404.1|

## Часть 3. Глобальная балансировка <a name="3"></a>

### Страны с наибольшей долей пользователей Slack. [^4]
|Регион | Количество пользователей |
|-------------|-------------|
|United States|193 055|
|United Kingdom|35 302|
|Europa|25 537|
|India|19 233|
|Canada|18 844|
|Australia|12 692|

### Расположение Дата-Центров

На основе предоставленных данных разместим дата-центры в следующих регионах для оптимального обслуживания пользователей Slack и уменьшения задержек: [^5]
- Соединенные Штаты – с наибольшим числом пользователей (193 055), 
необходимо развернуть основной или несколько дата-центров в этом регионе, чтобы обеспечить лучшую доступность большинству пользователей.
Расположим дата-центры в наиболее нагруженных точках США, а именно в Вашингтоне и Лос-Анджелесе.
- Европа – учитывая значительное присутствие во Франции (12 881) и Германии (12 656), которые вместе составляют более 25 000 пользователей, стоит развернуть вторичный дата-центр в одном из этих стран для лучшего обслуживания европейских пользователей.
Расположим дата-центр во Франкфурте, как в одном из крупнейших мест скопления трафика в регионе.
- Индия – учитывая так же не малое количество пользователей в регионе(19 233), Расположим один дата-центр в Мумбае как в одном из крупнейших мест скопления трафика в регионе.
- Австралия – учитывая так же не малое количество пользователей в регионе(12 692), Расположим один дата-центр в Сиднее как в одном из крупнейших мест скопления трафика в регионе.

### Глобальная балансировка

Так как дата-центры сильно удалены друг от друга, то будем использовать протокол BGP совместно с географически распределённой системой балансировки нагрузки.
Для использования BGP совместно с географически распределённой системой балансировки нагрузки, необходимо несколько шагов:

- Настройка нескольких экземпляров приложения в отдельных дата-центрах по всему миру. Каждый экземпляр должен иметь свой уникальный IP-адрес, который будет объявлен в BGP.
- Объявление IP-префиксов своих дата-центров в BGP с помощью своих автономных систем. В каждом дата-центре должны быть настроены собственные роутеры BGP, которые будут обмениваться информацией о доступных путях.
- Использование механизмов балансировки нагрузки, таких как GLB или GSLB, для распределения входящего трафика по географическим критериям. Эти системы используют DNS-записи для связывания доменных имён с конкретными IP-адресами, связанными с индивидуальными дата-центрами.

## Часть 4. Локальная балансировка <a name="4"></a>

На уровне ДЦ мы установим маршрутизатор, который будет распределять трафик через маршрутизацию BGP на L3 балансировщик.
#### L3 Балансировщик:
После маршрутизатора будут настроены виртуальные серверы через прямую маршрутизацию. Маршрутизатор отправляет запросы на виртуальные IP-адреса, и L3 балансировщик в Ethernet-фрейме изменяет MAC-адрес назначения на основе загрузки сервера, отправляя их на определенные L7 балансировщики.
После обработки L7 балансировщиком, ответы напрямую отправляются клиентам, минуя L3 балансировщик.
Для обеспечения высокой доступности используется VRRP/CARP на балансировщиках, а служба keepalived отслеживает состояние L7 балансировщика и уведомляет об этом L3 балансировщик.
#### L7 Балансировщик:
Envoy, заменит NGINX, позволяя кэшировать некоторые запросы и решая проблемы медленных клиентов.
С помощью Weighted Least Connections входящие запросы распределяются среди служб, запущенных в подах Kubernetes.
Отказоустойчивость внутри служб обрабатывается самим Kubernetes, в то время как для балансировщиков нагрузки используется Linux Heartbeat.
#### SSL:
Уровень завершения SSL расшифровывает входящие HTTPS-соединения до их достижения к серверам приложений, снимая нагрузку от шифрования с бэкэнд-серверов.
Кэш сессий хранит сеансы для улучшения производительности и опыта пользователя.
Эта конфигурация позволит эффективно распределять входящий сетевой трафик по всему миру с оптимальным использованием ресурсов в различных регионах, минимизируя задержки и максимизируя надежность.

## Часть 5. Логическая модель <a name="5"></a>
![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/3b2a1568-1702-46f8-a791-ab7ba2ae5047)


#### Таблица "Канал" (Channel)
Сохраняет информацию о канале. Каждый канал имеет уникальный идентификатор (id), имя (name), тип (type), описание (description) и идентификатор создателя (creator_id).

#### Таблица "Сообщения" (Messages):
Хранит информацию о сообщениях. Каждое сообщение имеет уникальный идентификатор (id), идентификатор отправителя (sender_id), временную метку (timestamp), содержание (content), идентификатор файла сообщения (message_file_id), и реакцию в виде эмодзи (reaction_emoji).

#### Таблица "Файлы" (Files):
Хранит информацию о файлах. Каждый файл имеет уникальный идентификатор (id), размер (size), а также идентификатор создателя (creator_id).

#### Таблица "Пользователи" (Users):
Хранит информацию о пользователях системы. Каждый пользователь имеет уникальный идентификатор (id), имя пользователя (username), адрес электронной почты (email), часовой пояс (timezone), аватар (avatar), дату регистрации (created_at) и идентификатор пароля (password_id).

#### Таблица "Сеансы" (Sessions):
Хранит информацию о текущих активных сеансах пользователей. Каждая сессия имеет уникальный идентификатор (session_id), идентификатор сессии (sid), идентификатор пользователя (user_id) и время окончания действия сеанса (expiration).

#### Таблица "Членство в каналах" (ChannelMemberships):
Хранит информацию о том, к каким каналам подключены пользователи. Каждый членство имеет уникальный идентификатор (id), идентификатор канала (channel_id), идентификатор пользователя (user_id) и время присоединения пользователя (joined_at).

#### Таблица "Проверенные сообщения" (MessageCheckeds):
Хранит информацию о том, какие сообщения были проверены пользователями. Каждая запись имеет уникальный идентификатор (id), идентификатор пользователя (user_id), идентификатор сообщения (message_id).

Требования к консистентности:

1. В таблице "Канал" уникальный первичный ключ "id". Каждый канал должен иметь уникальный идентификатор.
2. В таблице "Сообщения" уникальные пары комбинаций "sender_id" и "timestamp", чтобы избежать дублирования одного и того же пользователя, отправляющего несколько одинаковых сообщений в один момент времени.
3. В таблице "Файлы" уникальный первичный ключ "id". Каждый файл должен иметь уникальный идентификатор.
4. В таблице "Пользователи" уникальный первичный ключ "id". Каждый пользователь должен иметь уникальный идентификатор.
5. В таблице "Сеансы" уникальный первичный ключ "session_id". Каждая сессия должна иметь уникальный идентификатор.
6. В таблице "Членство в каналах" уникальная пара комбинации "channel_id" и "user_id", чтобы обеспечить точность привязки пользователей к каналам.
7. В таблице "Проверенные сообщения" уникальная пара комбинации "user_id" и "entity_id", чтобы обеспечить корректность учета просмотренных пользователем сообщений.

#### Размер одной записи для каждой таблицы:

1. `Channel` (28 байт + длина `name`, `type`, и `description`)
* `id`: 4 байт (integer)
* `name`: среднее кол-во 30 символов * 1 байт = 30 байт
* `type`: среднее кол-во 5 символов * 1 байт = 5 байт
* `description`: среднее кол-во 50 символов * 1 байт = 50 байт
* `creator_id`: 4 байт (integer)
Всего: 28 байт + длина `name`, `type`, и `description`.

2. `Message` (60 байт + длина `content`, `reaction_emoji`)
* `id`: 4 байт (integer)
* `sender_id`: 4 байт (integer)
* `timestamp`: 8 байт (timestamp)
* `content`: среднее кол-во 100 символов * 1 байт = 100 байт
* `reaction_emoji`: среднее кол-во 5 символов * 1 байт = 5 байт
Всего: 56 байт + длина `content` и `reaction_emoji`.

3. `File` (28 байт + длина `path`)
* `id`: 4 байт (integer)
* `size`: 4 байт (integer)
* `path`: среднее кол-во 50 символов * 1 байт = 50 байт
* `creator_id`: 4 байт (integer)
Всего: 28 байт + длина `path`.

4. `User` (96 байт + длина `username`, `email`, `avatar`)
* `id`: 4 байт (integer)
* `username`: среднее кол-во 20 символов * 1 байт = 20 байт
* `email`: среднее кол-во 30 символов * 1 байт = 30 байт
* `timezone`: среднее кол-во 10 символов * 1 байт = 10 байт 
* `avatar`: среднее кол-во 50 символов * 1 байт = 50 байт 
* `created_at`: 8 байт (timestamp)
* `password_id`: 4 байт (integer)
Всего: 96 байт + длина `username`, `email`, и `avatar`.

5. `Session` (52 байт)
* `session_id`: 4 байт (integer)
* `sid`: среднее кол-во 50 символов * 1 байт = 50 байт
* `user_id`: 4 байт (integer)
* `expiration`: 8 байт (timestamp)
Всего: 52 байт.

6. `ChannelMembership` (28 байт)
* `id`: 4 байт (integer)
* `channel_id`: 4 байт (integer)
* `user_id`: 4 байт (integer)
* `joined_at`: 8 байт (timestamp)
Всего: 28 байт.

7. `MessageChecked` (20 байт)
* `id`: 4 байт (integer)
* `user_id`: 4 байт (integer)
* `message_id`: 4 байт (integer)
Всего: 20 байт.

## Часть 6. Физическая модель <a name="6"></a>
|Таблица | СУБД |
| ------------- |-------------|
|Channel|Postgresql|
|Message|Cassandra|
|File|S3|
|Reaction|Cassandra|
|User|Postgresql|
|MessageChecked|ClickHouse|
|ChannelMembership|Postgresql|
|Chat|Postgresql|
|Session|Redis|
|Media|S3|
|Attachment|Cassandra|
|VectorMessage|Cassandra|



Можно индексировать следующие столбцы для ускорения запросов:
  
  - Таблица "Канал" (Channel): id, channel_id
  - Таблица "Сообщения" (Messages): sender_id, thread_id, entity_id
  - Таблица "Файлы" (Files): id
  - Таблица "Медиа" (Media): id
  - Таблица "Вложение" (Attachment): id, message_id
  - Таблица "Пользователи" (Users): id
  - Таблица "Членство в каналах" (ChannelMemberships): channel_id, user_id
  - Таблица "Проверенные сообщения" (MessageChecked): (user_id, entity_id)
  - Таблица "Вектор Сообщения"(VectorMessage): embedding_content

Шардинг можно провести по следующим критериям:

  - По id канала (Channel) - Шардинг можно организовать на основе уникального идентификатора канала. Это позволит распределить данные по нескольким шардам в зависимости от количества каналов, тем самым улучшая масштабируемость и производительность.
  - По id пользователя (Users) - Аналогично предыдущему случаю, здесь также возможна организация горизонтального шардинга на основе уникального идентификатора пользователя. Это позволит распределить данные по нескольким шардам в зависимости от количества пользователей, улучшая масштабируемость и производительность.
  - По id сообщения (Messages) - Для сообщений можно провести горизонтальный шардинг на основе уникального идентификатора, Это позволит распределить данные по нескольким шардам в зависимости от объёма сообщений, улучшая масштабируемость и производительность.

Из-за специфичности данных в вектором виде и отсутствия встроенных эффективных механизмов индексации таких данных будем использовать библиотеку Faiss, которая поддерживает HNSW индексацию, не смотря на то что индексация таким образом занимает больше памяти, но это значительно ускоряет поиск. Подробнее в Части 7.

Рапределение данных по шардам будет реализовано по принципу хэширования. При добавлении нового ключа данных в базу данных вычисляется его хэш-значение и на основании этого значения определяется номер шарда, на который он будет сохраняться. При чтении данных из базы данных необходимо вычислить хэш-значение ключа и определить номер шарда, на котором этот ключ должен находиться. Затем происходит чтение данных с указанного шарда.
Подробнее про алгоритм распределения в Части 7.

## Часть 7. Алгоритмы <a name="7"></a>
### Шардинг
Ring-based algorithms (кольцевые алгоритмы) — это класс алгоритмов, используемых для разделения данных между узлами в распределённых системах. Основная идея состоит в том, что узлы (шарды) располагаются на виртуальном кольце, а ключи данных хэшируются в точки на этом кольце, после чего данные сопоставляются с ближайшим узлом (шардом) по часовой стрелке.

Рассмотрим применение ring-based algorithms для каждого из перечисленных в части 6 случаев:

  По id канала (Channel):
        Каждый шард представляет собой узел на виртуальном кольце.
        Для каждого канала вычисляется hash-значение на основе его уникального идентификатора.
        Hash-значения каналов распределяются по виртуальному кольцу.
        Канал отправляется на шард, который соответствует ближайшей точке на кольце по часовой стрелке относительно hash-значения канала.

  По id пользователя (Users):
        Каждый шард представляет собой узел на виртуальном кольце.
        Для каждого пользователя вычисляется hash-значение на основе его уникального идентификатора.
        Hash-значения пользователей распределяются по виртуальному кольцу.
        Пользователь отправляется на шард, который соответствует ближайшей точке на кольце по часовой стрелке относительно hash-значения пользователя.

  По id сообщения (Messages):
        Каждый шард представляет собой узел на виртуальном кольце.
        Для каждого сообщения вычисляется hash-значение на основе его уникального идентификатора.
        Hash-значения сообщений распределяются по виртуальному кольцу.
        Сообщение отправляется на шард, который соответствует ближайшей точке на кольце по часовой стрелке относительно hash-значения сообщения.

### Hierarchical Navigable Small World (HNSW):
Начнем с родительского алгоритма NSW. Он основан на графе «мир тесен». Эти графы имеют любопытную и полезную нам особенность: пара вершин с большой вероятностью не смежна, но они достижимы за сравнительно небольшое число шагов log(N) (в среднем).
Для поиска соседей мы обходим граф в поисках вершин с минимальным расстоянием до запроса. Начинаем со случайной вершины, считаем расстояние от непосещенных вершин «друзей» (вершин, соединенных с текущей ребром) до запроса и переходим в вершину с наименьшим расстоянием. Длинные ребра придают графу свойства тесного мира и позволяют быстро перемещаться в область близких к запросу объектов, а короткие — жадно искать ближайших соседей.
![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/84593467-7455-4dc7-b44b-eaa04b245771)
По мере обхода графа обновляем небольшой список ближайших соседей и останавливаемся, если на очередной итерации список не обновился.

Рассмотрим развитие описанной выше идеи в алгоритме Hierarchical Navigable Small World (HNSW). Он во многом схож с NSW, однако теперь мы имеем дело с иерархией графов: на нулевом слое представлены все объекты, а по мере увеличения слоя — все меньшая и меньшая их подвыборка. При этом все объекты на слое n+1 есть и на слое n.

![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/ce5d0d16-3002-4870-811a-ac6a3bbbb906)

При поиске старт происходит со случайной вершины в графе верхнего слоя, там мы быстро находим близкие к запросу вершины (кандидаты) и возобновляем поиск с них на предыдущем слое.

### Поиск по сообщениям:
Для реализации поиска по сообщениям будет использоваться векторное представление слов. Преобразовывать сообщения в векторное представление будем с помощью алгоритма Word2Vec.
После представления сообщений в векторном виде возьмём вектор для фразы и поищем, какие вектора ближе всего в смысле косинусного расстояния с помощью cosineSimilarity встроенного в Cassandra.
Отдача результата поиска по принципу возрастания косинусного расстояния между векторами. Пример результата поиска.
![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/3c0fbd15-e1c2-437b-9efb-473de4d87282)


Word2Vec - это алгоритм машинного обучения, который используется для создания word embeddings (векторных представлений слов). Word2Vec позволяет представить слова в виде числовых векторов, которые отражают семантические и синтаксические связи между словами.

Принцип работы Word2Vec основан на идее, что слова, которые встречаются в похожих контекстах, имеют схожие значения. Алгоритм Word2Vec использует нейронные сети для обучения векторных представлений слов на больших текстовых корпусах.

### Weighted Least Connections:

Алгоритм Weighted Least Connections используется в балансировщиках нагрузки для распределения входящих запросов между серверами. Этот алгоритм учитывает не только количество активных соединений к каждому серверу, но и "вес" каждого сервера.

"Вес" сервера может быть определен вручную на основе его производительности, доступности ресурсов или других факторов. Сервер с большим весом может обрабатывать больше запросов, чем сервер с меньшим весом.

В работе алгоритма Weighted Least Connections каждый новый запрос направляется на сервер с наименьшим числом активных соединений, учитывая его вес.

Этот алгоритм позволяет более эффективно распределить нагрузку между серверами, учитывая их способность обрабатывать запросы. Это может быть особенно полезно в ситуациях, когда серверы имеют различную производительность или когда нагрузка на серверы меняется во времени.

## Часть 8. Технологии <a name="8"></a>
|Технология|Область применения|Мотивация|
| ------------- |-------------|-------------|
|VRRP (Virtual Router Redundancy Protocol)|Локальная балансировка| Поддержка виртуальной маршрутизации|
|Envoy|Проксирование|Был выбран т.к поддерживает большое кол-во протоколов таких как gRPC или WebSocket|
|React, Typescript|Frontend|Эффективность, большое сообщество|
|WebSocket|Соединение между клиентом и сервером|Позволяет обмениваться сообщениями между клиентом и сервером в режиме реального времени|
|gRPC|Общение между сервисами| Более эффективен для передачи данных между сервисами|
|ClickHouse|СУБД|Быстрый доступ к данным. Низкая стоимость хранения на Гб|
|Go|Backend| Достаточно быстрый и хорошо спроектированный язык|
|Postgresql|Sql СУБД|PostgreSQL предлагает больше возможностей, чем MySQL. Он обеспечивает большую гибкость в типах данных, масштабируемости, параллелизме и целостности данных.|
|Redis|БД Кэширования| Быстрый доступ к данным|
|Cassandra|СУБД|Используем для хранения сообщений и реакций|
|Kafka|Асинхронные запросы|Предоставляет единообразную платформу с высокой пропускной способностью и низкой задержкой для обработки данных в режиме реального времени.|
|Prometheus + Grafana|Метрики|Удобные и широко распространённые инструменты мониторинга.|
|Word2Vec|Поиск сообщений|Представление текстовых данных в векторном виде|
|Faiss|Индексация данных в векторном виде|Легко интегрируется с Cassandra и поддерживает HNSW|
|Kubernetes|Оркестрация Контейнеров|Эффективное управление ресурсами и удобное развёртывание|


## Часть 9. Схема проекта <a name="9"></a>
![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/3dfc3229-3f67-47aa-be41-00774adeea7e)
![изображение](https://github.com/JuFnd/highload-hw-slack/assets/109366718/3aaa9d7b-6a7c-4f3b-9342-1e025fe2989f)


## Часть 10. Обеспечение надёжности <a name="10"></a>
### Резервирование:
- Master-Standby репликация Postgresql, Cassandra в сервисах Channel и Chat(2 реплики).
- Master-Standby репликация Postgresql в сервисе Auth для таблицы User(2 реплики).
- Master-Standby репликация ClickHouse в сервисе Message для таблицы MessageChecked(2 реплики).
- Master-Standby репликация Kafka(2 реплики).

### Асинхронность:
Для защиты от сбоев у Kafka предусмотрена архитектура ведущий-ведомый на уровне раздела журнала, и в этой архитектуре ведущие называются лидерами, а ведомые еще могут называться репликами. Лидер каждого сегмента может иметь несколько ведомых. Если на сервере, где находится лидер, происходит сбой, предполагается, что реплика становится лидером и все сообщения сохраняются, только обслуживание на короткое время прерывается.

В случае сбоя лидера, одна из реплик автоматически становится новым лидером, что позволяет сохранить доступность и целостность данных. Достигается это следующим путём:

  - Создание топика с репликацией: При создании топика в Kafka, указываем фактор репликации, который определяет, сколько копий каждого сообщения будет храниться в кластере. Это делается с помощью параметра ```--replication-factor``` в команде создания топика. Например, команда ```kafka-topics --create --topic my_topic --replication-factor 3 --partitions 1 --zookeeper localhost:2181``` создаст топик с именем ```my_topic```, который будет хранить три копии каждого сообщения.
  - Настройка брокеров Kafka: Каждый брокер Kafka настраивается так, чтобы он мог стать лидером для любого из разделов. Это достигается путем установки параметра ```auto.leader.rebalance.enable``` в ```true``` в файле конфигурации брокера ```server.properties```.
  - Балансировка лидеров: Kafka предоставляет инструменты для мониторинга распределения лидеров по брокерам и их балансировки при необходимости. Это делается с помощью скрипта ```kafka-preferred-replica-election.sh```, который запускает процесс выбора предпочтительных реплик, чтобы сбалансировать количество лидеров на каждом брокере.
  - Обработка сбоев: В случае сбоя лидера, Kafka автоматически выбирает нового лидера из списка реплик. Этот процесс обычно происходит быстро, но его скорость может зависеть от различных факторов, таких как количество реплик и их текущее состояние.
  - Поддержание синхронизации реплик: Для обеспечения целостности данных важно, чтобы все реплики были синхронизированы с лидером. Kafka использует механизм ведущего-ведомого для поддержания синхронизации реплик, и можно контролировать, какие реплики считаются "в синхроне" с помощью параметра ```min.insync.replicas``` в файле конфигурации брокера.


### Отказоустойчивость:

#### Graceful shutdown:
Будет обрабатываться сигнал остановки сервиса. Приём новых запросов будет остановлен.
Дожидаемся выполнения запросов, которые уже успели дойти и только после этого отключаем сервис.
Так же для обеспечения отказоустойчивости на уровне бэкенда реализуем пинг базы раз в какое-то время.


  - Обработка сигналов: При получении сигнала SIGTERM от Kubernetes, наше приложение должно начать процесс Graceful Shutdown. Это может включать в себя завершение обработки текущих запросов, закрытие соединений с базами данных и другие задачи по очистке.
  - Настройка периода завершения: Kubernetes предоставляет период завершения (по умолчанию 30 секунд), в течение которого приложение может завершить свою работу перед тем, как будет принудительно остановлено с помощью SIGKILL. Этот период можно настроить в спецификации пода.
  - Использование хуков жизненного цикла: Kubernetes предоставляет хуки жизненного цикла, которые позволяют запускать определенные команды на различных этапах жизненного цикла пода. Мы можем использовать хук preStop для запуска скрипта или команды, которая начнет процесс Graceful Shutdown.
  - Удаление из балансировщика нагрузки: При начале процесса Graceful Shutdown, под должен быть удален из балансировщика нагрузки, чтобы новые запросы не были направлены на него. Kubernetes автоматически удаляет под из Endpoint'ов службы при получении сигнала SIGTERM.
  - Тестирование и мониторинг: Важно тщательно протестировать процесс Graceful Shutdown и мониторить его в продакшене, чтобы убедиться, что он работает корректно и не приводит к потере данных или прерыванию работы.


#### Graceful degradation:
Основное назначение Slack возможность оперативно обмениваться с коллегами информацией для эффективной командной работы.

##### Следующие сервисы должны работать для обеспечения надёжности продукта:
   - Чаты: При проблемах с сервисом чатов потенциальное сообщение будет сохраняться и пользователю со стороны фронтенда будет предложено повторить отправку либо удалить сообщение. Получение старых сообщений можно реализовать через сервис поиска сообщений если он доступен.
   - Каналы: При проблемах с сервисом каналов, можно использовать кэшированные данные для отображения списка каналов и их истории.
   - Авторизация: Если сервис авторизации недоступен, можно использовать токены доступа, которые были выданы ранее, и продолжать предоставлять доступ к сервису. Это, конечно, предполагает, что токены доступа имеют достаточно долгий срок жизни.

##### Допускается потеря части функционала:
   - Поиск по сообщения: В случае отказа сервиса поиска, можно перенаправить запросы на поиск на прямую в бд с сообщениями, в случае если это возможно.
   - Реакции на сообщения: В случае перегрузки сервиса сообщений, можно отключить сервис реакций.

### Логгирование и Мониторинг:

#### Логгирование:
Для быстрого отслеживания запросов будем использовать паттерн RequestId.
Для хранения логов будет использоваться MongoDB т.к логи будем сохранять в виде JSON.

#### Мониторинг:
Сбор метрик будет реализован следующим образом так как в Envoy нет прямого аналога конфигурации NGINX Exporter. Однако существуют другие способы получения метрик Envoy и их экспорта в Prometheus.
Envoy предоставляет встроенный Prometheus metrics exporter, который позволяет собирать метрики посредством HTTP endpoint. Чтобы воспользоваться этим функционалом, необходимо включить prometheus статусный прокси в конфигурационном файле Envoy.

Конфиг Prometheus
```
global:
  scrape_interval:     15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'envoy'
    static_configs:
      - targets: ['localhost:9901']
```

Конфиг Envoy
```
http_servers:
- name: "main"
  ...
  routes:
  - match: { prefix: "/metrics" }
    route:
      cluster: "cluster_0"
  listeners:
  - name: "listener_0"
    ...
    filter_chains:
    - filters:
      - name: "prometheus"
        config: {}
```
## Часть 11. Расчёт ресурсов <a name="11"></a>

### Базовый расчёт аппаратных ресурсов
|Service|RPS|CPU|RAM|NET|
|------|-------|-------|-------|------|
|User|168.61|2|16ГБ|4.4 ГБит/c|
|Auth|168.4|2|16ГБ|4 ГБит/c|
|Message|4620.3|64|128ГБ|24.6 ГБит/c|
|Channel|458.3|12|64ГБ|9.2 ГБит/c|
|Chat|345.6|12|64ГБ|8 ГБит/c|
|Reaction|114.1|6|32ГБ|3.6 ГБит/c|
|Search|578.7|12|32ГБ|10.1 ГБит/c|

Расчёты сделаны из предположения 1 ядро CPU на 100 RPS
Возьмём по 2 сервера на ДЦ на случай отказа одного из них.

### Конфигурация серверов
|Service|Hosting|Configuration|Cores|Cnt|Buy|Rent per month|
|-------|-------|-------------|----|----|------|------|
|User|own|Intel Xeon E3-1220 v6 OEM/4x8Gb/4xNVMe8Тb|4|12|11400$|500$|
|Auth|own|Intel Xeon E3-1220 v6 OEM/4x8Gb/4xNVMe8Тb|4|12|11400$|500$|
|Reaction|own|Intel Xeon E3-1220 v6 OEM/6x8Gb/4xNVMe8Тb|4|6|15600$|700$|
|Search|own|Intel Xeon Gold 6256 OEM/6x8Gb/6xNVMe8Тb|12|12|15600$|700$|
|Chat|own|AMD EPYC 7763 SP3 OEM/8x16Gb/6xNVMe8Тb|12|12|26700$|900$|
|Channel|own|AMD EPYC 7763 SP3 OEM/8x16Gb/6xNVMe8Тb|12|12|26700$|900$|
|Message|own|AMD EPYC 7763 SP3 OEM/8x16Gb/6xNVMe8Tb|12|12|26700$|900$|

Slack существует с 1992 года, однако резкий рост нагрузки наступил в период с 2019 по н.в, то возьмём период за 5 лет.

#### Покупка
```
11400$ * 2 * 12 + 15600$ * 12 + 15600$ * 6 + 26700$ * 3 * 12 = 1 515 600$
```

#### Аренда
Почитаем стоимость в месяц.
```
500 * 2 * 12 + 700 * 12 + 700 * 6 + 900 * 3 * 12 = 57 000$ per month.
```

Посчитаем стоимость за 5 лет
```
57000 * 12 * 5 = 3 420 000$
```
Из расчётов становится ясно, что аренда менее выгодна чем покупка серверов.

### Источники
[^1]: [MAU/DAU](https://www.statista.com/statistics/1025213/worldwide-slack-active-users/)
[^2]: [Detailed_statistics](https://techjury.net/blog/slack-statistics/)
[^3]: [Activity](https://www.sostav.ru/publication/we-are-social-i-hootsuite-52472.html)
[^4]: [Countries](https://colorlib.com/wp/slack-statistics/)
[^5]: [Trafic](https://global-internet-map-2022.telegeography.com/)
