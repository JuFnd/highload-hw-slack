# Проектирование высоконагруженных сервисов
Курсовая работа в рамках 3-го семестра программы по Веб-разработке ОЦ VK x МГТУ им. Н.Э. Баумана (ex. "Технопарк") по дисциплине "Проектирование высоконагруженных сервисов"

#### Автор - [Ислам Османов](https://park.vk.company/profile/is.osmanov/ "Страница на портале VK x МГТУ")
#### Задание - [Методические указания](https://github.com/init/highload/blob/main/homework_architecture.md)

#### Содержание:
1. [Тема, функционал и аудитория](#1)
2. [Расчёт нагрузки](#2)
3. [Глобальная балансировка](#3)

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
|Создание чатов|34.92 млн|
|Создание каналов|34.92 млн|
|Создание тредов|34.92 млн|
|Создание папок|34.92 млн|
|Реакции|34.92 млн|

- По статистике пользователь остаётся онлайн в среднем 9 часов. Исходя из предоставленных данных, мы знаем, что в системе в среднем авторизовано 9 часов в сутки и что в сутки активно 38.8 млн пользователей. Чтобы выяснить, сколько в среднем авторизуется пользователей в сутки, мы можем использовать эти данные. Если каждый пользователь в среднем проводит в системе 9 часов, то общее количество часов, проведенных всеми активными пользователями в сутки, составляет
38.8 млн * 9 часов = 349.2 млн часов. Так как в сутках 24 часа, то общее количество авторизаций пользователей в сутки можно вычислить, разделив общее количество часов, проведенных всеми активными пользователями, на количество часов в сутках. То есть 349.2 млн часов / 24 часа = 14.55 млн авторизаций в сутки.[^2]
- После пандемии потребность в онлайн платформах ведения бизнеса выросла и Slack не стал исключением. После 2020-го года наблюдается активный рост новых пользователей. На основе статистических данных можно получить среднюю дневную активность пользователей за 5 лет ((47.2 - 38.8) + (38.8 - 32.3) + (32.3 - 25.7) + (25.7 - 19.2) + (19.2 - 12.6)) /  5 = 6.92 млн. 
Средняя дневная активность пользователей на сайте за 5 лет увеличилась на 6.92 млн пользователей в год. Это означает, что каждый день количество активных пользователей увеличивается примерно на 6.92 млн / 365 дней = 18920 новых пользователей в день.[^1]
- В месяц отправляется 1.5 миллиарда сообщений из этого следует, что в день в среднем отправляется приблизительно 1.5 млрд / 30 дней = 50 млн.[^2]
- Так как не удалось найти информации об отправке файлов за день предположим, что в день на 50 сообщений приходится 10 файлов. Тогда в день будет отправлено 10 млн файлов.
- В среднем человек проводит в соц. сетях 2 часа 25 минут, что составляет около 30 минут в час => таким образом пользователь заходит в соц сети 6-7 раз в день. Теперь, чтобы выяснить, сколько сообщений в среднем просматривает один пользователь в день, мы можем разделить общее количество сообщений в день (50 млн) на общее количество авторизаций в сутки (14.55 млн). Получим примерно 3.4 сообщения на одну авторизацию. Если учесть, что пользователь заходит в соцсети примерно 6-7 раз в день, то в среднем один пользователь просматривает около 20-24 сообщений в день. Теперь 24 * 14.55 млн = 349.2 млн просмотренных сообщений в день.[^3]
- Так как не удалось найти информации о создании чатов/каналов/папок/тредов и реакций предположим, что на данные действия приходится 10% остальной активности т.к эти действия как правило выполняются один раз на конкретный кейс. Возьмём имеющийся у нас максимум по активности, а именно 349.2 млн => 349.2 млн * 0.1 = 34.92 млн. 

### Технические метрики

### Cредний размер хранилища на пользователя

|Хранилище | Стартовый размер (Пб) | Увеличение (Пб/год) |
| ------------- |-------------|-------------|
|Сообщения|0,052214|0,052214|
|Файлы|73|73|
|Данные пользователя|11,2566|11,2566|

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
- Память, используемая для пользователей 6.92 млн * 2Мб * 365 = 30840000 * 365 = 11256600000 МБ = 11256600 ГБ = 11,2566 ПБ
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
|Страна | Количество пользователей |
|-------------|-------------|
|United States|193 055|
|United Kingdom|35 302|
|India|19 233|
|Canada|18 844|
|France|12 881|
|Australia|12 692|
|Germany|12 656|

### Источники
[^1]: [MAU/DAU](https://www.statista.com/statistics/1025213/worldwide-slack-active-users/)
[^2]: [Detailed_statistics](https://techjury.net/blog/slack-statistics/)
[^3]: [Activity](https://www.sostav.ru/publication/we-are-social-i-hootsuite-52472.html)
[^4]: [Countries](https://colorlib.com/wp/slack-statistics/)
