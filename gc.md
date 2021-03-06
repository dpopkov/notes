Garbage Collection
------------------

* [1 - Чем Java отличается от C++?](#q01)
* [2 - Что такое менеджер памяти?](#q02)
* [3 - Какой механизм используется в Java для управления памятью?](#q03)
* [4 - Опишите процесс работы сборщика мусора?](#q04)
* [5 - Какие алгоритмы сборщика вы знаете?](#q05)
* [6 - Чем отличаются сборщики мусора?](#q06)
* [7 - Расскажите про утилиты для анализа памяти?](#q07)
* [8 - Что такое ссылки?](#q08)
* [9 - Какие типы ссылок вы знаете?](#q09)
* [10 - Чем они отличаются?](#q10)
* [10.1 - Для чего используется WeakHashMap и ReferenceQueue?](#q1001)
* [11 - Расскажите про String pool, Int pool.](#q11)
* [12 - Расскажите про String.intern](#q12)
* [13 - Расскажите, что такое профайлер.](#q13)
* [14 - Расскажите, как использовать VisualVM.](#q14)
* [15 - Расскажите, чем отлицается sampling от profiling? Это типы аудита. Режим работы в профайлере.](#q15)
* [16 - Расскажите о benchmark. Приведите примеры кода.](#q16)
* [17 - Расскажите о методе finalized.](#q17)
* [18 - Расскажите о методе clone. Deep clone and shallow clone.](#q18)
* [19 - Расскажите о Stack, Heap.](#q19)
* [Monitoring Utilities](#utils)


1 - Чем Java отличается от C++?<a name="q01"></a>
-------------------------------------------------
В языках типа С память для объекта выделяется и потом должна быть освобождена вручную. Если ее не освободить, то будет утечка памяти. 
В Java, когда объект больше не используется, сборщик мусора освобождает память занимаемую объектом.
То есть в С++ и других языках с ручным управлением памятью от программиста требуется понимание точного времени жизни каждого объекта в системе, тогда как в Java среда выполнения сама отслеживает объекты и избавляется от тех объектов, котороые больше не нужны и автоматически освобождает память.


2 - Что такое менеджер памяти?<a name="q02"></a>
-----------------------------------------------
Менеджер памяти - часть компьютерной программы, обрабатывающая запросы на выделение и освобождение оперативной памяти. Основное назначение менеджера памяти - выделение динамической памяти, то есть памяти под объекты создаваемые во время выполнения программы.
Менеджер памяти может быть системный, встроенный в ОС, либо являться частью библиотеки языка программирования или частью среды исполнения.
Во многих языках и средах исполнения менеджер памяти также снимает с программиста ответственность за освобождение памяти и берет это на себя, что устраняет ошибки связанные с отсутствием освобождения занятой памяти (утечки) или наоборот преждевременным освобождением памяти.


3 - Какой механизм используется в Java для управления памятью?<a name="q03"></a>
--------------------------------------------------------------


4 - Опишите процесс работы сборщика мусора?<a name="q04"></a>
-------------------------------------------
Перед сборкой мусора все потоки приложения приостанавливаются на время сбора - событие Stop-the-world (STW).
Затем в соответствии с алгоритмом сборщика, объекты на которые не ведут ссылки утилизируются, а выжившие объекты эвакуируются в единую смежную область памяти, за счет чего достигается дефрагментация.
При этом все сборщики используют _слабую гипотезу о поколениях_, которая гласит что вероятность смерти снижается очень быстро при увеличении возраста, то есть значительная часть объектов умирает еще в молодом возрасте. Поэтому сборщик сосредотачивается в первую очередь на тех объектах, которые созданы недавно.


5 - Какие алгоритмы сборщика вы знаете?<a name="q05"></a>
---------------------------------------
Алгоритм Mark-and-Sweep (Маркировки и Выметания), использует список указателей на каждый объект, для которого была выделена память.
1) сброс меток у всех объектов
2) начиная с локальных и статических объектных переменных находит все живые объекты
3) устанавливает метку у всех найденных живых объектов
4) проходит по списку всех размещенных в памяти объектов и для тех объектов у которых метка не установлена освобождает память и удаляет объект из списка размещенных в памяти объектов.


6 - Чем отличаются сборщики мусора?<a name="q06"></a>
-----------------------------------
Сборщик мусора может быть:
Последовательный или Параллельный, Concurrent vs Stop-the-World, а также 
с Уплотнением или без, либо Копирующий.  

Serial (Последовательный), -XX:+UseSerialGC - использует 1 поток, STW и сборка мусора методом Mark and Sweep, потом копирование для young gen и уплотнение для old gen, подходит для небольших приложений на клиенте.

Parallel (Параллельный), -XX:+UseParallelGC - использует тот же алгоритм сборки что и Последовательный, но в несколько потоков для minor collection и один поток для full garbage collection. Использует те же Eden, S0, S1, old space. Подходит для использования на серверах так как имеет меньше STW events.

Parallel Old, -XX:+UseParallelOldGC - то же что и Parallel, но использует несколько потоков также для полной сборки. Предпочтительнее, чем ParallelGC.

Concurrent Mark and Sweep (CMS), -XX:+UseConcMarkSweepGC, для young gen использует тот же алгоритм что и другие. На сборке old gen делает короткие остановки для маркировки части живых объектов, а поиск остальных и чистку выполняет параллельно с работающим приложением, за счет чего общее время остановки сборщика значительно уменьшается. Не является уплотняющим, а управляет списками свободной памяти в фрагментированном хипе, и старается разместить объекты в свободных фрагментах. Имеет меньшую задержку на полной сборке, но требует больше памяти.
В настоящее время deprecated.

G1 - замена CMS. Разделяет хип на регионы. Также имеет Eden, Suvivor и Old space, но это логическое разделение. Сборка произодится не на всем поколении, а только на части регионов, которые можно очистить не превышая желаемого времени, выбирая те, где скопилось наибольшее количество мусора. Часть сборок делает смешанными, где кол-во регионов выбирается исходя из статистики, чтобы не превышать время сборки. Может быть уязвим к короткоживущим объектам большого размера. 

Для выбора сборщика нужно проводить тестирование в условиях максимально приближенных к условиям использования.

7 - Расскажите про утилиты для анализа памяти?<a name="q07"></a>
----------------------------------------------
__jps__ - показывает pid работающих jvm процессов.  
__jmap__  показывает кол-во памяти выделенное каждому типу и кол-во экземпляров.  
Примеры:  
`jmap -histo:live <pid>` - показывает гистограмму объектов  
Такую же информацию предоставляет вкладка Sampler в VisualVM.  
`jmap -dump:[live,] file=/path/to/dump <pid>` - сохраняет дамп хипа на диск  
Дамп можно также получить с помощью VisualVM.  
Для более точного анализа используются журналы сборки мусора.

__jstat__ - утилита позволяющая мониторить статистику JVM включая сборку мусора.  
`jstat -option <pid> <interval> <count>`
Разные опции позволяют оследить кол-во прошедших сборок, время затраченное на сборки, размер и использование памяти.
Утилита дает возможность проверить как приложение реагирует на использование разных
сборщиков.

__jconsole__ - графическая утилита для мониторинга и управления приложениями.  

__YourKit Profiler__ - платное приложение.  

Object Histogram - моментальный снимок - список показывающий сколько памяти занято объектами разных типов без учета взаимосвязей между объектами.

VisualVM с плагином VisualGC - позволяет увидеть в реальном времени что происходит в jvm, распределение памяти, графики заполнения разделов хипа, количество и время сборок мусора.

7.1 - Что такое Heap Dump?
--------------------------
Heap Dump - это сохраненная на диск копия живых объектов находящихся в хипе приложения. Может иметь очень большой размер (гигабайты). Содержит отношения между объектами, то есть то, чего не предоставляет гистограмма объектов.
Может быть получен с помощью __jmap__:  
`jmap -dump:[live,] file=/path/to/dump <pid>`  
а также VisualVM, или опций запуска java:  
-XX:+HeapDumpOnOutOfMemoryError  
-XX:HeapDumpPath=/path/to/dump  
Однако нужно принимать в расчет возможный размер дампа, который может быть очень велик.


7.2 - Как можно проанализировать Heap Dump?
------------------------------------------
[Free Heap Dump Analyzer](http://eclipse.org/mat)  
Other Tools: JVisualVM, jhat.  


8 - Что такое ссылки?<a name="q08"><a/>
---------------------
Ссылка это переменная указывающая на объект. Обычные переменные ссылочного типа получают с помощью операции по созданию нового объекта с ключевым словом new.
Кроме того есть специализированные ссылочные типы, которые хранят исходную ссылку на объект, но обрабатываются GC иначе, чем обычные ссылочные переменные.

9 - Какие типы ссылок вы знаете?<a name="q09"></a>
--------------------------------
Strong Reference > Soft Reference > Weak Reference > Phantom Reference
Объект не собираются GC если на него есть Strong reference.
Объект может быть собран GC если на него есть Soft, Weak or Phantom Reference.

10 - Чем они отличаются?<a name="q10"></a>
------------------------
Strong Reference - обычные ссылки на объекты. Объект не собирается сборщиком, пока не освобождены все strong references на него. 

Soft Reference - объект переживает сборку если памяти хватает, но он  может быть собран GC при условии нехватки памяти. Все Soft References гарантированно будут собраны перед OutOfMemoryError. Могут использоваться для простого кэширования объектов, когда основная ссылка обнуляется, но ссылка на объект хранится в Soft Reference до тех пор пока не возникнет недостаток памяти, и тогда будет собрана. Пока объект не собран, ссылку на него можно вернуть, после сборки возвращает null.

Weak Reference - объект всегда собирается во время сборки GC. 
Может использоваться для ассоциирования с объектом метаданных или дополнительных данных с помощью WeakHashMap.

Phantom Reference - в отличие от Soft и Weak, Phantom не дает исходной ссылки на объект, даже если объект еще жив.
Используется для взаимодействия с GC, мониторинга когда объект собирается, заменяет использование метода finalize
Метод get() всегда возвращает null.

10.1 - Для чего используется WeakHashMap и ReferenceQueue?<a name="q1001"></a>
---------------------------------------------------------
Ключ является Weak Reference на оъект, а значением может быть метаданные объекта.
Когда на объект нет strong ссылок, ключ и значение освобождаются, то есть могут быть собраны сборщиком.

ReferenceQueue передается в конструкторя при создании ссылочного объекта. Это используется для ассоциирования механизма очистки с объектом. Для PhantomReference это обязательный параметр. После того как на объект нет более strong ссылок, существующие не-strong ссылки на это объект добавляются в ассоциированную с ними ReferenceQueue. Имеет poll() and remove() методы.
Когда strong ссылки очищены, gc добавляет оставшиеся от них reference объекты в очередь, затем они удаляются из очереди с последующим вызовом метода clean, к тому моменту уже есть уверенность что исходный объект собран GC.


11 - Расскажите про String pool, Int pool.<a name="q11"></a>
------------------------------------------
Благодаря неизменяемости строк, JVM оптимизирует кол-во памяти отданное строкам тем, что сохраняет только одну строку в пуле. Когда в исходном коде создается строка, то пул просматривается в поиске таких же строк. Если найдет, то вернет ссылку, если нет, то добавит строку в пул строк. В отличие от этого строки созданные как new всегда будут новыми объектами.


12 - Расскажите про String.intern<a name="q12"></a>
---------------------------------
Метод intern либо возвращает ссылку на аналогичную строку, если она уже есть в пуле строк, либо помещает строку в пул строк и возвращает ссылку на строку в пуле строк.
В java6 интерналайзд строки были частью permgen - области которая не очищалась сборщиком, начиная с java7 они стали частью хипа (в 7-й old gen, а в java8 сам permgen в хипе был заменен на metaspace), то есть могут быть собраны сборщиком мусора.


13 - Расскажите, что такое профайлер.<a name="q13"></a>
------------------------------------
???

### Memory Profiling
* event driven
* see patterns of activity
* records memory allocations and related information

### Memory Profilers

#### JVisualVM (tab Profiler)
* Free, Open Source Tool
* Can disable escape analysis (optimization that JVM uses to reduce allocations)

#### Java Mission Control
* Free for use in development
* More accurate


14 - Расскажите, как использовать VisualVM.<a name="q14"></a>
-------------------------------------------


14.1 - Что такое утечка памяти и из-за чего она может происходить в Java?
-------------------------------------------------------------------------
Утечка памяти происходит когда память выделена для размещения объектов, но после того как объекты уже больше не нужны, эта память не освобождается.
В Java виртаульная машина выделяет память для объекта в момент его создания с помощью ключевого слова new.
Если ссылка хранится в локальной переменной, то при выходе из метода она перестает существовать, и следовательно объект может быть собран. Однако если ссылка является полем, то она существует в течении жизни объекта, частью которого она является, следовательно сборщик мусора не может собрать объект, на который она указывает, даже если это объект уже не нужен. Следовательно это ответствнность программиста, а не сборщика мусора - освобождать те ссылки, которые более не нужны.


15 - Расскажите, чем отлицается sampling от profiling? Это типы аудита. Режим работы в профайлере.<a name="q15"></a>
--------------------------------------------------------------------------------------


16 - Расскажите о benchmark. Приведите примеры кода.<a name="q16"></a>
----------------------------------------------------


17 - Расскажите о методе finalized.<a name="q17"></a>
----------------------------------


18 - Расскажите о методе clone. Deep clone and shallow clone.<a name="q18"></a>
-------------------------------------------------------------


19 - Расскажите о Stack, Heap.<a name="q19"></a>
------------------------------



Monitoring Utilities<a name="utils"></a>
====================

java
----
`-Xmx<size>`  - set the maximum heap size  
`-Xms<size>`  - set the starting heap size  
`-Xmn<size>`  - set the initial and maximum size for young gen  
`-verbose:gc` - включает режим логирования сборок мусора в stdout   
`-Xloggc:filename` - указывает имя файла для логирования, имеет приоритет над -verbose:gc.  
`-XX:+PrintGCTimeStamps` - добавляет к информации о сборках временные метки (в виде количества секунд, прошедших с начала работы программы)  
`-XX:+PrintGCDetails` - включает расширенный вывод информации о сборках мусора  
`-XX:+PrintFlagsFinal` - при старте приложения выводит в stdout значения всех опций, заданных явно или установленных самой JVM.
`-XX:+HeapDumpOnOutOfMemoryError` - create a heap dump file in current working directory.  
`-XX:+PrintCommandLineFlags` - показать опции с которыми запускается JVM.  

jps
---
You use the jps command to list the instrumented JVMs on the target system.  
Options: -mlvV  
* -m displays the arguments passed to the main method. The output may be null for embedded JVMs.
* -l displays the full package name for the application's main class or the full path name to the application's JAR file.
* -v displays the arguments passed to the JVM.
* -V suppresses the output of the class name, JAR file name, and arguments passed to the main method, producing a list of only local JVM identifiers.

jinfo
-----
Выводит информацию (system properties, VM flags and arguments) об указанном jvm процессе.


