## Телеграмм бот для анкетирования

### Желающим присоединиться к разработке

Присоединяйтесь к группе [Телеграмма](https://t.me/+k349Hrrf4-0wMTli)

### Общее

При создании бота рекомендуется запретить его добавление в группы и не включать инлайн для глобальной видимости.

### Параметры для запуска

|Параметр| Значение                                                                                      | Пример                 |
|-------|-----------------------------------------------------------------------------------------------|------------------------|
|urls | Урлы, по которому доступен API                                                                | "http://localhost:9002" |
|dbPath | Путь, к папке, где хранятся данные бота                                                       | tmp                    |
|botToken | Токен, выдаваемый BotFather                                                                   |                        |
|pathToQuest| Путь к файлу с шаблоном анкеты                                                                | sample.csv             |
|adminID| Id пользователей через запятую, которые станут администраторами                               | `12345,345345, 123452`   |
| nextcloudUrl | В случае интеграции с Nextcloud Deck - адрес сервера, если интеграции нет, можно игнорировать | `https://nexcloud.net` |
| nextcloudLogin | Логин в Nextcloud, обязателен, если указан `nextcloudUrl`                                     | `login`                |
| nextcloudPass | Сервисный пароль учетки в Nextcloud, обязателен, если указан `nextcloudUrl`                   | `password`             |
| nextcloudDeckIndex | Номер доски, на которой бот создаст список входящих карточек и куда он будет их помещать      | `1`                    |

Пример запуска под windows:

```
RQ.Bot.exe --urls="http://localhost:9002" --dbPath=tmp --botToken=xxxxxxx:yyyyyyyyyyyyy --pathToQuest=sample.csv
```

### API

Все запросы к API требуют заголовка `X-Volunteer-Token`, с токеном, который выдается только админам самим ботом.

### Администраторы

Администратором могут получить только пользователи, указанные в `adminID` через запятую.

### Шаблон анкеты

Виды колонок на текущий момент

| Колонка | Описание                                                                                                                                                                                | Значения                                                                                                  |
|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Text    | Текст вопроса, должен быть уникальным для текущей анкеты                                                                                                                                | см. ниже                                                                                                  |
| ValidationRegex    | Регулярное выражение для проверки ответа на вопрос                                                                                                                                      | см. ниже                                                                                                  |
| DuplicateCheck    | По ответам в этой графе идет поиск дубликатов среди остальных анкет с целью подсвечивания их в конечной выгрузке в XLSX                                                                 | `0` или `1`                                                                                               |
| Group    | Объединяет вопросы с одинаковыми значениями `Group` для того чтобы их можно было пропускать отрицательным ответом на вопрос с `IsGroupSwitch`                                           | Любое целое число                                                                                         |
| IsGroupSwitch    | Положительный ответ на вопрос с этим модификатором открывает доступ ко всем вопросам с таким же значением `Group`                                                                       | `0` или `1`                                                                                               |
| AutopassMode    | Режим автоматического прокручивания сообщений                                                                                                                                           | `0` - не прокручивать, `1` прокрутить по порядку в анкете, `2` прокрутить в начале `3` прокрутить в конце |
| Attachment    | Для картинок - или ссылка на изображение или абсолютный путь на файловой системе где лежит изображение, которое нужно прикрепить к вопросу, для видео - только путь на файловой системе | `https://i.redd.it/gg2rx1axksp91.jpg` или `/etc/photo_2022-09-24_14-48-20.jpg`                            |
| Category    | Категория, в которой должен размещаться вопрос                                                                                                                                          | см. ниже                                                                                                  |


Файл с шаблоном анкеты должен быть представлен в формате csv c заголовками `Text` и `ValidationRegex`.
В поле `Text` вписывается вопрос, а в `ValidationRegex` регулярное выражение для проверки правильности ввода.
С помощью регулярок можно контролировать ввод чисел, дат, номеров телефонов, категорий и т.д.
Если ввод может быть произвольным, то `ValidationRegex` можно оставить пустым.


`Category` определяет пункт меню, в котором находится указанный вопрос. Символ `->` разделяет пункты в иерархии.
В примере ниже создается меню следущего вида

|           |
|-----------| 
|Главное    |
|Потребности|

В секции `Главное` еще одно подменю

| Главное    |
|------------| 
| Персональное    |
| Местоположение |

*ВАЖНО* Текс вопроса является его же уникальным идентификатором.
Если вы отредактируете в уже существующей анкете текст вопроса, то в выгрузке могут быть дубли из старых вопросов.

Пример шаблона с меню категорий:

```csv
Text,Group,IsGroupSwitch,DuplicateCheck,Category,ValidationRegex
"Обязательный вопрос 1",0,0,0,,
"Обязательный вопрос 2",0,0,0,,
"Обязательный вопрос 3",0,0,0,,
"Обязательный вопрос 4",0,0,0,,
"Ваше ФИО:",0,0,0,"Главное->Персональное"
"Какого вы года рождения?",0,0,1,"Главное->Персональное","\d{4}"
"Где вы находитесь?",0,0,0,"Главное->Местоположение"
"Нужна одежда?",1,1,0,"Потребности"
"Размер одежды",1,0,0,"Потребности"
"Тип одежды",1,0,0,"Потребности"
"Нужен мотоцикл?",2,1,0,"Потребности"
"Размер мотоцикла",2,0,0,"Потребности"
"Марка",2,0,0,"Потребности"
"Нужно жилье?",3,1,0,"Потребности"
"Сколько человек",3,0,0,"Потребности"
"Регион",3,0,0,"Потребности"
```

Пример шаблона без меню, с обязательными и необязательными вопросами:

```csv
Text,Group,IsGroupSwitch,DuplicateCheck,Category,ValidationRegex
"Обязательный вопрос 1",0,0,0,,
"Обязательный вопрос 2",0,0,0,,
"Обязательный вопрос 3",0,0,0,,
"Обязательный вопрос 4",0,0,0,,
"Ваше ФИО:",0,0,0,
"Какого вы года рождения?",0,0,1,,"\d{4}"
"Где вы находитесь?",0,0,0,
"Нужна одежда?",1,1,0,
"Размер одежды",1,0,0,
"Тип одежды",1,0,0,
"Нужен мотоцикл?",2,1,0,
"Размер мотоцикла",2,0,0,
"Марка",2,0,0,
"Нужно жилье?",3,1,0,
"Сколько человек",3,0,0,
"Регион",3,0,0,
```

