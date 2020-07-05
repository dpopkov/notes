Что такое concurrency и parallelism?
------------------------------------
Concurrency это способность выполнять несколько задач одновременно, либо в чередующиеся моменты времени.
Parallelism это способность использовать несколько процессоров для решения одной задачи за более короткое время.

Чем отличается процесс от потока?
---------------------------------
Процесс это независимый объект ОС, которому выделены системные ресурсы (время и память).
Потоки это независимые последовательности исполнения.
Главное отличие в том, что потоки (одного процесса) исполняются в общем пространстве памяти,
тогда как процессы исполняются в изолированных адресных пространствах памяти.
Также:
* Поток это часть процесса.
* Процесс может состоять из нескольких потоков.
* Процесс имеет собственное адресное пространство. Поток использует память потока и делит ее
с других потоками процесса.
* Поток может коммуницировать с другими потоками этого же процесса напрямую через heap. 
Поток может коммуницировать с другими потоками через меж процессную коммуникацию.
* Новые потоки создатся легко. Однако создание нового процесса означает создание новой JVM.


Каким образом можно создать поток?
----------------------------------
Способы создания потока:
* Имплементация Runnable
* Расширение Thread с переопределением метода run()
* 


Какие способы синхронизации в Java?
-----------------------------------


Как работают методы sleep, yield?
---------------------------------



Как работают методы wait, notify, notifyAll?
--------------------------------------------


Объясните термины: монитор, мьютекс, критическая секция
-------------------------------------------------------


Как работает метод Tread.join()
-------------------------------


Что такое Deadlock. Приведите примеры.
--------------------------------------



На каком объекте происходит синхронизация при вызове static synchronized метода?
-------------------------------------------------------------------------------



Различия между Collections.synchronizedMap(new HashMap()) и ConcurrentHashMap
-----------------------------------------------------------------------------


Различия между интерфейсами Runnable и Callable
-----------------------------------------------


Различия между isInterrupted() и interrupted()
----------------------------------------------


Почему методы wait и notify вызываются в синхронизированном блоке?
------------------------------------------------------------------


Что происходит при вызове Thread.interrupt()?
--------------------------------------------


Перечислите Все причины по которым может быть выброшено InterruptedException
----------------------------------------------------------------------------


Назовите отличия synchronize{} и ReentrantLock
----------------------------------------------


Приведите наиболее существенное отличие между CountDownLatch и CyclicBarrier
----------------------------------------------------------------------------


Отличие Thread.start() и Thread.run()
-------------------------------------


Объясните volatile
------------------


Расскажите про приоритеты потока
--------------------------------


Что такое потоки-демоны?
------------------------


Назовите все возможные состояние потока
---------------------------------------
Состояния потока (enum Thread.State):
* NEW - новый поток который еще не стартовал
* RUNNABLE - исполняющийся поток, начинает через некоторе время после вызова start()
* BLOCKED - ожидает на критеческой секции (wating for a monitor lock)
* WAITING - неопределенное ожидание пока другой поток не выполнит определенное действие
* TIMED_WAITING - ожидание с заданным таймаутом
* TERMINATED - поток завершивший работу (перезапустить нельзя)


Что такое race condition
------------------------


Как остановить нить
-------------------


Что происходит, когда в нити появляется исключение?
---------------------------------------------------
Если исключение не поймано, то нить мертва. 
Если установлен обработчик непойманных исключений, он будет использован.
Когда нить собирается остановиться из-за непойманного исключения, то JVM проверит наличие обработчика и если обработчик Thread.UncaughtExceptionHandler установлен, то будет вызван метод uncaughtException с передачей ему нити и исключения.


Что такое TreadLocal переменная
-------------------------------


Что такое FutureTask
--------------------


Что такое Thread pool
---------------------


Различие между livelock и deadlock
----------------------------------


Как проверить удерживает ли нить lock
-------------------------------------


Как получить дамп нити
----------------------


Какой JVM параметр используется для контроля размера стека нити
---------------------------------------------------------------


Что такое Semaphore
-------------------


Что будет, если очередь пула нитей уже заполнена, а вы подадите задачу
----------------------------------------------------------------------


Чем отличаются submit от execute у ExecutorService
--------------------------------------------------


Что такое блокирующий метод
---------------------------


Что такое double checked locking Синглетона
-------------------------------------------


Что такое фрейворк Fork/Join
----------------------------


Чем отличается shutdown от shutdownNow у ThreadPoolExecutor
-----------------------------------------------------------


Как создать ThreadPool у ExecutorService только на 1, на 5, на неограниченное кол-во потоков
--------------------------------------------------------------------------------------------


Что такое ReadWriteLock
-----------------------


В чем отличие Thread от FutureTask? В чем отличие Thread.interrupt() и FutureTask.cancel()
-----------------------------------------------------------------------------------------


Расскажите про шаблон Producer Consumer
---------------------------------------




