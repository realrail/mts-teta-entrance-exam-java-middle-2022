# Сервис по планированию задач

## Список фичей:

1. Пользователь может добавлять, удалять, закрывать и заново открывать задачи
2. Названия задач должны быть уникальными для всех пользователей (удаленные не учитываются).
3. Пользователь может получить список всех задач любого другого пользователя, кроме удаленных.
4. Пользователь может закрывать, удалять и заново открывать только свои задачи
5. Задача проходит следующие состояния: `CREATED <--> CLOSED -> DELETED`. При этом задача в
   статусе `CREATED` не может сразу перейти в `DELETED`. Задача же в `DELETED` больше не может
   переходить ни в какое состояние.

Веб-сервер принимает сообщения по придуманному нами протоколу - **Simple Web Protocol**.

Запросы и ответы состоят из одной строки.

Формат запроса: “**USER COMMAND ARG**”

Формат ответа: “**RESULT ARG**”

## Описание формата:

1. `USER` - имя пользователя, который осуществляет действия
2. `COMMAND` - команда. Варианты команд:
    1. `CREATE_TASK MyTask`- создать задачу с названием MyTask
    2. `DELETE_TASK MyTask` - удалить задачу MyTask
    3. `CLOSE_TASK MyTask` - закрыть задачу MyTask
    4. `REOPEN_TASK MyTask` - заново открыть задачу MyTask
    5. `LIST_TASK USER` - Получить список задач пользователя
3. `ARG` - аргумент запроса или ответа. Может отсутствовать.
4. `RESULT` - ответ сервера о совершении действия. Варианты ответов.
    1. `CREATED` - задача успешно создана
    2. `DELETED` - задача успешно удалена
    3. `CLOSED` - задача успешно закрыта
    4. `REOPENED` - задача успешно открыта заново
    5. `TASKS [MyTask1, MyTask2]` - список задач пользователя. Если задач нет, список пустой (`[]`)
    6. `WRONG_FORMAT` - Неверный формат запроса
    7. `ACCESS_DENIED` - Нет прав на совершение операции
    8. `ERROR` - Любая другая ошибка
5. Все команды, а также имена пользователей **регистрозависимые**
6. Запросы валидируются в следующем порядке: формат запроса, право на совершение операции, все
   остальные проверки. Если первая проверка не прошла, остальные не выполняются.

## Примеры запросов и ответов:

1. `VASYA CREATE_TASK CleanRoom`
    1. `CREATED`
2. `PETYA DELETE_TASK CleanRoom`
    1. `ACCESS_DENIED`
3. `PETYA CREATE_TASK Task1`
    1. `CREATED`
4. `PETYA CREATE_TASK Task2`
    1. `CREATED`
5. `VASYA LIST_TASK PETYA`
    1. `TASKS [Task1, Task2]`
6. `VASYA CREATE_TASK CleanRoom`
    1. `ERROR`

## Definition of Done

В `ServerTest` необходимо добавить тест-кейсы, которые проверят корректность вышеописанных фич.

Все данные храним в оперативной памяти. Concurrency-эффекты не учитываем, так как сервер
обрабатывает запросы последовательно.

Spring, прочие фреймворки и вспомогательные библиотеки использовать **нельзя**. Применяем только то, что уже есть в проекте. 

## Дополнительные баллы

Учитывается общее оформление кода, архитектурное разделение компонентов, а также наличие Unit-тестов
на отдельные части проекта.

## Порядок сдачи

1. Форкаем репозиторий.
2. Создаем новую ветку от `master`.
3. Выполняем задание и пушим изменения.
4. Создаем Pull Request из своей feature-ветки в `master` **исходного** репозитория.
5. В комментарии оставляем ФИО и почту (тегаем @SimonHarmonicMinor)
6. Ждем результатов :)

Для приема задания необходимо, чтобы билд проходил успешно (команда `./mvn package`). Перед
отправкой изменений, пожалуйста, проверьте, что команда отрабатывает локально.
