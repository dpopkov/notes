Date and Time API
=================
__(not finished)__

java.time.Instant
-----------------

Класс `java.time.Instant` (Мгновение) представляет точку на линии времени, начало координат которой приходится на 1 января 1970 года на Гринвичском меридиане. Минимальное и максимальное значение `java.time.Instant` примерно равно миллиарду лет в обе стороны от начала координат.

Текущий момент можно получить методом `Instant.now()`. Мгновения можно сравнивать по `equals` и `compareTo`. Для определения разницы можно использовать `java.time.Duration.between(a, b)`. Например:

```Java
Instant start = Instant.now();
runLongAlgorithm();
Instant end = Instant.now();
Duration timeElapsed = Duration.between(start, end);
long milliseconds = timeElapsed.toMillis();
```

