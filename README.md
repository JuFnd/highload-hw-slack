# Проектирование высоконагруженных сервисов
Курсовая работа в рамках 3-го семестра программы по Веб-разработке ОЦ VK x МГТУ им. Н.Э. Баумана (ex. "Технопарк") по дисциплине "Проектирование высоконагруженных сервисов"

#### Автор - [Ислам Османов](https://park.vk.company/profile/is.osmanov/ "Страница на портале VK x МГТУ")
#### Задание - [Методические указания](https://github.com/init/highload/blob/main/homework_architecture.md)

#### Содержание:
1. [Тема, функционал и аудитория](#1)
2. 
### Тема курсовой работы - **"Проектирование бизнес-мессенджера"**
В качестве примера и аналога выбран Slack.
Slack представляет собой бизнес-мессенджер, объединяющий людей с необходимой им информацией, превращая таким образом способ общения организаций. Основной особенностью Slack является поддержка тредов, что позволяет в рамках одного канала или чата создавать независимые ветки обсуждений, облегчая организацию и структурирование коммуникации. Кроме того, Slack интегрируется с большим количеством других служб, позволяя пользователям управлять проектами и задачами непосредственно из мессенджера. Являясь мощным средством коммуникации и сотрудничества, Slack легко адаптируется под потребности любой команды или организации.

## Часть 1. Тема и целевая аудитория <a name="1"></a>

### Ключевой функционал сервиса
- Авторизация
- Каналы: Открытые или закрытые каналы для различных проектов, отделов или тем, чтобы сохранять беседы организованными. Участники могут присоединяться, уходить или быть добавлены другими как требуется.
- Прямые сообщения (ПМ): Переписка с конкретным участником.
- Обмен файлами и хранение: Легко делитесь файлами, такими как изображения, документы, видео, электронные таблицы и т. д., непосредственно с компьютера.   
- Уведомления и Напоминания: Настройка персональных уведомлений на основе ключевых слов, упоминаний, каналов или прямых сообщений. Напоминания для установки дат окончания, повторов или других важных задач.
- Поиск: Находить прошедшие разговоры, файлы и информацию во всех каналах и прямых сообщениях с помощью мощного поискового функционала.
- Конструктор Рабочих Процессов: Создавать автоматизированные рабочие процессы с помощью интуитивно понятных drag-and-drop интерфейсов, доступных в Конструкторе Рабочих Процессов.

### Целевая аудитория
- MAU 65M [^1]

### Статистика
[^1]: MAU(https://www.statista.com/statistics/1025213/worldwide-slack-active-users/)

