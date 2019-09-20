Quartz Tutorial
===============


1 - Обзор
---------
Quartz это фреймворк планировщик задач с открытым исходным кодом написанный полностью на Java и предназначенный для использования в J2SE и J2EE приложениях.

Вы можете создать сложные расписания для исполнения любых заданий. Например задания, которые выполняются ежедневно, каждую пятницу в 19:30 или в последний день каждого месяца.

Пример использования Quartz:
```Java
SchedulerFactory schedFact = new org.quartz.impl.StdSchedulerFactory();
Scheduler sched = schedFact.getScheduler();
sched.start();

// define the job and tie it to our HelloJob class
JobDetail job = JobBuilder.newJob(HelloJob.class)
    .withIdentity("myJob", "group1")
    .build();

// Trigger the job to run now, and then every 40 seconds
Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("myTrigger", "group1")
    .startNow()
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(40)
        .repeatForever())
    .build();

// Tell quartz to schedule the job using our trigger
sched.scheduleJob(job, trigger);
```

2 - Maven зависимости
---------------------
```xml
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.0</version>
</dependency>
```

3 - Quartz API
--------------

Главным компонентом является `Scheduler` (Планировщик). От ответственен за управление runtime окружением для приложения.

Для обеспечения масштабируемости, Quartz основан на многопоточной архитектуре. __После старта фреймворк инициализирует набор рабочих потоков__, которых использует `Scheduler` (Планировщик) для выполнения `Job`s (Заданий).

Благодаря этому фрейморк может выполнять множество `Job`s (заданий) одновременно. Он также полагается на набор слабо связанных `ThreadPool` компонентов управляющих окружение потоков.

Ключевыми интерфейсами API являются:  
* `Scheduler` (Планировщик) - главный интерфейс для взаимодействия с планировщиком.
* `Job` (Задание) - интерфейс, который должен быть имплементирован компонентом, который мы хотим запустить.
* `JobDetail` - используется для задания экземпляров `Job`s.
* `Trigger` - компонент задающий расписание согласно которому `Job` будет выполняться.
* `JobBuilder` - используется для создания экземпляров `JobDetails` определяющих экземпляры `Job`s.
* `TriggerBuilder` - используется для создания экземпляров `Trigger`.


4 - Scheduler (Планировщик)
---------------------------
Для создания экзпемляра планировщика используется фабрика `SchedulerFactory`:

```Java
SchedulerFactory schedulerFactory = new StdSchedulerFactory();
Scheduler scheduler = schedulerFactory.getScheduler();
```

Жизненный цикл `Scheduler` (Планировщика) начинается с его создания с помощью `SchedulerFactory` и заканчивается вызовом метода `shutdown()`. С момента создания объект `Scheduler` может быть использован для добавления, удаления и получения списка объектов `Job` и `Trigger`, а также выполнения других операций связанных с пранировщиком, таких как постановка триггера на паузу.  
Важно помнить, что __`Scheduler` не выполняет никаких действий над триггерами пока он не запущен методом `start()`__.

```Java
scheduler.start();
```


5 - Jobs (Задания)
------------------

Задание это объект класса имплементирущего инферфейс `Job`. Он имеет только один простой метод:
 ```Java
public class SimpleJob implements Job {
    public void execute(JobExecutionContext arg0) throws JobExecutionException {
        System.out.println("This is a quartz job!");
    }
}
```
Когда срабатывает триггер Задания, то метод `execute()` вызывается в одном из рабочих потоков планировщика.

Объект `JobExecutionContext` переданный в метод, представляет информацию об экземпляре задания, такую как его runtime окружение, ссылку на запускающий его Планировщик, ссылку на его Триггер, ссылку на объект `JobDetail` и другую информацию.

Объект `JobDetail` создается клиентом Quartz во время добавления Задания планировщику. Это в сущности создание экземпляра Задания.

```Java
JobDetail job = JobBuilder.newJob(SimpleJob.class)
    .withIdentity("myJob", "group1")
    .build();
```

Это объект может также содержать различные property (свойства, атрибуты) Задания, а также `JobDataMap`, которая может быть использована для хранения информации о состоянии конкретного экземпляра Задания.


5.1 - JobDataMap
----------------

`JobDataMap` используется для хранения любого количества данных необходимых экземпляру Задания. `JobDataMap` является имплементацией интерфейса `Map` и имеет дополнительные методы для сохранения и получения значений примитивных типов.

Пример сохранения данных в `JobDataMap` во время создания объекта `JobDetail`, перед добавлением Задания в Планировщик:

```Java
JobDetail job = newJob(SimpleJob.class)
    .withIdentity("myJob", "group1")
    .usingJobData("jobSays", "Hello World!")
    .usingJobData("myFloatValue", 3.141f)
    .build();
```

А также пример получения этих данных во время выполнения Задания:

```Java
public class SimpleJob implements Job { 
    public void execute(JobExecutionContext context) throws JobExecutionException {
        JobDataMap dataMap = context.getJobDetail().getJobDataMap();
 
        String jobSays = dataMap.getString("jobSays");
        float myFloatValue = dataMap.getFloat("myFloatValue");
 
        System.out.println("Job says: " + jobSays + ", and val is: " + myFloatValue);
    } 
}
```

Можно также добавить setter методы в класс Задания, которые соответствуют именам ключей в `JobDataMap`.  
If we do this, Quartz’s default JobFactory implementation automatically calls those setters when the job is instantiated, thus preventing the need to explicitly get the values out of the map within our execute method.


6 - Triggers
------------

Триггеры используются для инициации исполнения Задания.

Для запланированного исполнения Задания, необходимо создать триггер и настроить его свойства в соответствии с необходимостью запланированного выполнения.

```Java
Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("myTrigger", "group1")
    .startNow()
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(40)
        .repeatForever())
    .build();
```

Триггер также может иметь ассоциированный объект `JobDataMap`, что полезно для передачи Заданию параметров, которые специфичны для исполнения триггера.

Есть различные типы триггеров для разных планировочных нужд. Каждый имеет различные атрибуты `TriggerKey` для отслеживания их идентичности. Некоторые атрибуты являются общими для всех типов:
* атрибут `jobKey` соответствует идентификатору задания, которое должно быть начато в момент сигнала триггера.
* атрибут `startTime` показывает когда расписание триггера начинает действовать. Его значение имеет тип `java.util.Date` и определяет момент для календарного времени. Для одних типов триггеров, триггер срабатывает во время старта. Для других это время старта расписания.
* атрибут `endTime` показывает когда расписание триггера будет отменено.

Чаще всего используются триггеры типов `SimpleTrigger` и `CronTrigger`.


6.1 - Приоритет
---------------

Иногда, при наличии большого кол-ва триггеров, Quartz не имеет достаточно ресурсов чтобы немедленно запускать все задания в одно и тоже время. Для контроля приоритетности служит атрибут `priority`.

Например, если 10 триггеров должны сработать в одно и тоже время, но доступно только 4 рабочих потока, то будут выполнены 4 триггера с наибольшим приоритетом. Приоритет по умолчанию равен 5. Значение приоритета может быть любым целым числом, положительным или отрицательным.

Пример двух триггеров, из которых triggerA имеет больший приоритет:
```Java
Trigger triggerA = TriggerBuilder.newTrigger()
    .withIdentity("triggerA", "group1")
    .startNow()
    .withPriority(15)
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(40)
        .repeatForever())
    .build();
             
Trigger triggerB = TriggerBuilder.newTrigger()
    .withIdentity("triggerB", "group1")
    .startNow()
    .withPriority(10)
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(20)
        .repeatForever())
    .build();
```


6.2 - Misfire инструкции
------------------------

Если триггер пропускает время запуска по причине остановленного планировщика или отсутствия доступных рабочих потоков в пуле, то происходит misfire.

Разные типы триггеров имеют разные доступные им misfire инструкции. По умолчанию это smart policy инструкция. Когда планировщик стартует, он ищет misfired триггеры, после чего он обновляет их в соответствии с их индивидуальными misfire инструкциями.

Пример:
```Java
Trigger misFiredTriggerA = TriggerBuilder.newTrigger()
    .startAt(DateUtils.addSeconds(new Date(), -10))
    .build();
             
Trigger misFiredTriggerB = TriggerBuilder.newTrigger()
    .startAt(DateUtils.addSeconds(new Date(), -10))
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withMisfireHandlingInstructionFireNow())
    .build();
```

Триггеры должны был стартовать 10 секунд назад, это симуляция misfire, произошедшего по какой-либо причине.

Триггер misFiredTriggerA не имеет заданной инструкции, поэтому применяется smart policy и в этом случае вызывается withMisfireHandlingInstructionFireNow(). Это значит, что задание выполняется сразу после обнаружения планировщиком misfire.

Триггер misFiredTriggerB задает инструкцию withMisfireHandlingInstructionFireNow() явным образом, то есть результат тот же как при smart policy.


6.3 - SimpleTrigger
-------------------

`SimpleTrigger` используется в сценариях когда нужно выполнить задание в определенным момент времени. Однажды или повторно через заданные интервалы.

Например выполнение задания 13-го января 2018 года ровно в 12:20. Либо начать в это время и повторить 5 раз с интервалом в 10 секунд.

Пример триггера в заданное время:
```Java
SimpleTrigger trigger = (SimpleTrigger) TriggerBuilder.newTrigger()
    .withIdentity("trigger1", "group1")
    .startAt(myStartTime)
    .forJob("job1", "group1")
    .build();
```
Пример триггера с повтором:
```Java
SimpleTrigger trigger = (SimpleTrigger) TriggerBuilder.newTrigger()
    .withIdentity("trigger2", "group1")
    .startAt(myStartTime)
    .withSchedule(simpleSchedule()
        .withIntervalInSeconds(10)
        .withRepeatCount(10))
    .forJob("job1") 
    .build();
```

6.4 - CronTrigger
-----------------

`CronTrigger` используется если нужно использовать календарь. Например каждую пятницу в полдень или каждый рабочий день в 9:30.

Для конфигурирования `CronTrigger` используются cron-expressions. Они состоят из строк содержащих до 7 под-выражений.

Пример триггера срабатывающего каждую вторую минуту между 8 и 17 часами ежедневно:
```Java
CronTrigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trigger3", "group1")
    .withSchedule(CronScheduleBuilder.cronSchedule("0 0/2 8-17 * * ?"))
    .forJob("myJob", "group1")
    .build();

```
