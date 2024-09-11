# Действия

Действия - это специальные объекты, описываемые на стороне бэкенда и обрабываемые на стороне
Flutter, служащие для описания того, что будет происходить при взаимодействии с некоторыми
интерактивными элементами UI (ElevatedButton, GestureDetector, Checkbox, etc).

## Интерактивные элементы

Не для всех виджетов Duit может быть назначенно собственное действие, поскольку некоторые из них
могут служить лишь для создания UI, но не обрабатывают взаимодействие с собой в базовом случае (
Сontainer, etc).

### Список реализованных виджетов, к которым можно привязать действие

- DuitElevatedButton
- DuitGestureDetector
- DuitCheckbox
- DuitRadio
- DuitSlider
- DuitLifecycleStateListener
- DuitTextField
- DuitSwitch

## Типы действий

Разные типы действий позволяют фреймворку определить, каким образом должно быть выполнено действие:
используя транспортный слой, локально или в изолированной среде выполения (скрип).

| Тип                | Описание                                                                                                                                                                                                                                                                   |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Через транспорт    | Действие вызывает указанный эндпоинт на бэкенде и получает событие в качестве ответа. Пример: обычный http-запрос с неким payload. Если запрос выполняется с методом GET, объект payload будет преобразован в query params, в остальных случаях используется body запроса. |
| Локальное действие | Действие, с которым изначально связано некое событие, которое обрабатывается незамедлительно                                                                                                                                                                               |
| Скрипт             | Действие, содержащее исходный код скрипта для интегрированной вами среды выполнения. На выходе ожидает описание действия, которое будет обработано после завершения выполения скрипта                                                                                      |

## Зависимости действия

Действия могут иметь зависимости. Это означает, что такое действие способно собирать необходимые
значения из моделей связанных виджетов.

Ряд классов атрибутов интерактинвых виджетов может хранить в себе свое текущее значение, обновлять
его и отдавать его по запросу. За это поведение отвечает
класс [`AttendedModel<T>`](https://github.com/Duit-Foundation/flutter_duit/blob/main/lib/src/ui/models/attended_model.dart).

Список наследников `AttendedModel`:

- MetaAttributes
- SliderAttributes
- SwitchAttributes
- CheckboxAttributes
- RadioGroupContextAttributes
- TextFieldAttributes

[Логика метода сбора атрибутов и формирования payload](https://github.com/Duit-Foundation/flutter_duit/blob/c1d3cb326517ba155eb540727d5ad588fe5ae085/lib/src/duit_impl/driver.dart#L267)

## Создание действия

Подробнее разберем создание действия на примере `HttpAction`.

```typescript
HttpAction(
        "/textInput1change", // endpoint
        {
            method: "GET" // action metainfo, valid for http actions
        }, 
        [ // array of action dependencies
            {
                id: "textInput1", // id of the target widget
                target: "value" // property key at resulting object
            },
            {
                id: "textInput2", 
                target: "value2"
            },
            {
                id: "textInput3", 
                target: "value3"
            },
        ],
    )
```

- Первое, что требуется указать - это эндроинт, к которому будет образаться транспортный слой при
  выполнеии.
- Второй параметр - объект содержащий метаинформацию для действия. По умолчанию учитывается лишь
  свойсов `method`. Если оно явно не указано, для запроса будет использован метод GET.
- Третий параметр - массив зависимостей действия.

## Скрипты

Для начала использования скриптов требуется связать с фреймворком скриптовый движок, который будет
исполнять переданный код. Это делается через реализацию класса [DuitScriptRunner](https://github.com/Duit-Foundation/duit_kernel/blob/main/lib/src/misc/script_runner.dart).

Пример реализации расширения и подключения движка представлен в [репозитории](https://github.com/Duit-Foundation/duit_hetu_extension). 



