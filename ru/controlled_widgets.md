# Концепция управляемых виджетов и обновление состояния UI

### Введение

В рамках Duit все виджеты могут иметь две основные реализации: контроллируемые и неконтроллируемые.
Говоря о том, что виджет является контроллируемым, мы подразумеваем то, что данные виджета могут
быть обновлены в
следствии обработки события обновления, после чего он будет перестроен.

Под капотом контроллируемые виджеты это всегда StateFull виджеты и явное определение виджета, как
конроллируемого - необходимая оптимизация, чтобы не создавать лишнюю нагрузку используя
исключительно StateFull виджеты.

Если виджет является контроллируемым, для него создается отдельный UiController, который
присоединяется к UiDriver и обеспечивает обновление атрибутов виджета при необходимости и позволяет
выполнять связанные действия.

### Виджеты, которые являются контроллируемыми по умолчанию:

Некоторые виджеты по умолчанию всегда являются контроллируемыми в связи с тем, что обеспечивают
обработку пользовательского ввода, дозапрашивают данные для наполнения или просто выполняют
связанные действия.

- ElevatedButton
- ListView.builder & ListView.separated
- Checkbox
- Component
- GestureDetector
- LifecycleStateListener
- Meta
- Radio
- Subtree
- Slider
- Switch
- TextField
- AnimationBuilder

### Creating a controlled Widget

To create a controlled widget, just add `controlled:true` to the parameters
widget builder functions.

```typescript
Text(
    {
        attributes: {
            data: "Hello world!",
        },
        id: "text1",
        controlled: true,
    }
),
```

For the controller to work correctly, it is also necessary to pass the `id` parameter, which is
responsible for searching
and using the desired controller and widget point update.

### Example of updating UI using UpdateEvent

To update a controlled widget you need to know its `id`. Based on this we create an object
updates, where the list key is `id` and the value is the new attributes object.

```typescript
const event = UpdateEvent(
    {
        "text1": {
            "data": "Hellow Duit!"
        }
    },
    <...>
)
```