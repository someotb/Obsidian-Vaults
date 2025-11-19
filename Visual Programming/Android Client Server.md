# Объяснение неочевидных моментов в коде 

## Безопасные вызовы и дефолтные значения
- `?.` — безопасный вызов. Если объект равен `null`, выполнение не продолжается и возвращается `null`.
- `?:` — оператор Элвиса, задаёт значение по умолчанию, если левый операнд `null`.

```kotlin
val value = obj?.property ?: defaultValue
```

## Утверждение, что объект не null
- `!!` используется для утверждения, что объект не `null`. Если объект всё же `null`, произойдёт `NullPointerException`.

```kotlin
val nonNullValue = nullableValue!!
```

## Различие между CellInfoLte
- `android.telephony.CellInfoLte` — встроенный класс Android API для информации о LTE-ячейках.
- `com.example.mycal.model.CellInfoLte` — ваша модель данных для сериализации и передачи на сервер.

## Периодические задачи
- `Timer.scheduleAtFixedRate` — создаёт отдельный поток и выполняет задачу периодически.
- `Handler.postDelayed` — выполняет задачу в основном потоке через Handler.

```kotlin
periodicTimer.scheduleAtFixedRate(0, 1000) {
    // код для повторяющейся отправки данных
}
```

## ZeroMQ контекст и паттерн REQ/REP
- `ZMQ.context(1)` — создаёт контекст ZeroMQ с 1 I/O потоком.
- `SocketType.REQ` — тип сокета для запроса (request), ожидает ответа (reply) от сервера.

```kotlin
val zmqContext = ZMQ.context(1)
val socket = zmqContext.socket(SocketType.REQ)
socket.connect("tcp://10.0.2.2:2222")
socket.send(jsonString)
val reply = socket.recvStr()
```

## Проверка версии Android API
- `Build.VERSION.SDK_INT` позволяет выполнять код в зависимости от версии Android.

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
    // код для Android 11+
}
```

## Jetpack Compose модификаторы
- `Modifier.fillMaxSize()` — растягивает элемент на весь доступный размер.
- `Modifier.weight(1f)` — задаёт пропорциональную долю пространства в Column/Row.
- `Modifier.padding(16.dp)` — задаёт отступы.

```kotlin
Column(
    modifier = Modifier.fillMaxSize().padding(16.dp),
    verticalArrangement = Arrangement.SpaceBetween
) {
    // дочерние элементы
}
```

