Что такое поток ввода-вывода?
-----------------------------

Java программы выполняют ввод-вывод с помощью потоков. Поток это абстракция, которая производит или потребляет информацию. Поток связан с физическим устройством с помощью системы ввода-вывода Java. Потоки ведут себя одинаково, даже если конкретные физические устройства различаются. Поэтому одни и те же классы и методы ввода-вывода могут применяться к различным типам устройств. Это означает, что поток ввода может абстрагировать различные типы ввода: из файла, клавиатуры или сетевого сокета. Аналогично, поток вывода может ссылаться на файл, консоль или сетевое соединение. Потоки позволяют работать с вводом-выводом без учета различий между разнообразными устройствами.

JIONIO-23
Поток - это упорядоченная последовательность байтов (или символов) произвольной длины. 
Байты поступают через поток ввода из источника в приложение и через поток вывода из приложения в место назначения. Другими словами приложение читает из потока ввода и пишет в поток вывода. Источником или приемником данных может быть файл, сокет, буфер в памяти.

JTCR-641
Данные поступают в программу из потока ввода. Результат работы программы посылается в поток вывода. Физические устройства, предоставляющие или потребляющие данные, представляются в программе абстракцией потока данных. Поток ввода-вывода - это логическая сущность, которая производит или потребляет данные. Поток ввода-вывода связан с физическим устройством системой ввода-вывода Java.

A:
Поток ввода-вывода - это абстракция, связывающая данные потребляемые или производимые программой с неким источником. Различные источники или физические устройства могут быть связаны с потоком ввода-вывода, интерфейс которого позволяет работать без учета различий между этими устройствами. Конкретным источником или устройством может быть файл в файловой системе, либо сетевой сокет, либо буфер в памяти. 
Поток ввода-вывода абстрагирует ввод-вывод, связанный с конкретным устройстом и позволяет работать с ним посредством одних и тех же стандартных библиотечных классов и методов.
Сам поток данных выглядит как упорядоченная последовательность байтов (или символов) произвольной длины. При этом байты читаемые приложением предоставляются потоком ввода, а результат работы приложения пишется в поток вывода.
Помимо байтовых потоков ввода-вывода существуют также символьные потоки, а также потоки трансформирующие байты в другие типы данных. Потоки также могут осуществлять дополнительные функции, например сжание, шифрование и т.п.


Что такое Java IO?
------------------
java.io - это пакет в стандартной библиотеке Java, предоставляющей поддержку операций ввода-вывода основанных на потоках данных, сериализации и файловой системе.
Байтовые потоки являются наследниками абстрактных классов InputStream, OutputStream. 
Примеры классов:
FileInputStream/FileOutputStream - доступ к файлам,
ByteArrayInputStream/ByteArrayOutputStream - доступ к массиву байтов,
BufferedInputStream/BufferedOutputStream - буферизация ввода-вывода.


Что такое Java NIO?
-------------------
java.nio - New I/O, альтернативное API.
Работа основана на Channels and Buffers. Данные читаются из канала в буфер или пишутся из буфера в канал.

Может работать в неблокирующем режиме. Thread может попросить канал считать данные в буфер. Пока канал считывает данные в буфер, thread может делать что-то другое. Когда данные считаны в буфер, thread может продолжить обработку данных.



Что такое Scanner?
------------------
Scanner - класс в пакете java.util, представляющий собой простой текстовый сканер, способный парсить примитивные типы и строки, используя регулярные выражения. 
Вводом для сканера может служить InputStream, файловый объект или последовательность символов (строка или объект Readable).


Как работает Scanner внутри?
----------------------------
Сканер считывает входной поток и разделяет ввод на токены, которые представляют собой строки символов разделенные разделителем (по умолчанию пробел). Полученные токены могут либо быть непосредственно возвращены как строки, либо сканер может конвертировать токен в различные примитивные типы, если токен имеет подходящий формат. Различные методы next возвращают либо сам токен, либо значение соответствующего примитивного типа. 
Дефолтный разделитель может быть заменен любым другим с помощью метода useDelimeter(String or Pattern).


Какие базовые методы существуют в Scanner?
------------------------------------------
Методы hasNext(), next(), а также аналогичные методы для примитивных типов (boolean, byte, short, int, long, float, double), а также BigDecimal/BigInteger. 
Также delimiter(), useDelimeter(String/Pattern), radix(), useRadix().
findInLine(String/Pattern) - пытается найти соответствие указанному шаблону регулярного варажения до перевода строки игнорируя разделители, 
Stream<String> tokens() - возращает стрим токенов.


Что такое байтовый поток? Как он реализован внутри?
---------------------------------------------------
Байтовый поток представляет собой упорядоченную последовательность байтов произвольной длины. Реализация потока зависит того, с каким устройством ассоциирован данный поток. В любом случае последовательные вызовы метода read() без параметров, будут возвращать int значения байтов от первого до последнего доступного байта в потоке. 
Если поток реализован полностью на Java, например ByteArrayInputStream, который основан на массиве, то метод read() возвращает очередной байт в массиве и увеличивает индекс. 
Если же байтовый поток привязан к устройству, например FileInputStream, то будут будут использоваться нативные методы, то есть JVM будет обращаться к функциям операционной системы поверх которой JVM работает.


Что такое символьный поток? Как он реализован внутри?
-----------------------------------------------------
В отличие от байтового потока символьный поток позволяет читать или писать символы, поэтому соответсвующие методы read/write оперируют символами, а не байтами.

Если символьный поток использует байтовый поток, то он производит декодирование или кодирование между символами и байтами с использование определенной кодировки (заданной либо по умолчанию).
FileReader extends InputStreamReader -> StreamDecoder -> CharsetDecoder
FileWriter extends OutputStreamWriter -> StreamEncoder -> CharsetEncoder


Что такое буферизированный поток?
---------------------------------
Буферизированный поток (БП) считывает или записывает данные порциями определенного размера и использует для промежуточного хранения выделенный для этого буфер. Таким образом чтение или запись данных производится блоками, что более эффективно, чем отдельными байтами. 
Для использования БП необходим внутренний поток, который непосредственно читает или пишет данные. Он передается параметром в конструктор БП. 
При чтении из БП данные берутся из буфера, если вышли за его пределы, то буфер заполняется снова. 
При записи в БП, если буфер полон, то его содержимое пишется во внутренний поток, если нет, то запись ведется в буфер.
К байтовым БП относятся BufferedInputStream / BufferedOutputStream, к символьным - BufferedReader / BufferedWriter.


Что такое форматированный вывод? Какие механизмы позволяют осуществлять форматированный вывод?
----------------------------------------------------------------------------------------------
Форматированный вывод - это символьный вывод с помощью задания определенного шаблона форматирования, который определяет выравнивание, ширину вывода, особенности форматирования чисел, дат или денежных единиц с учетом региональных настроек.

В классах PrintStream и PrintWriter для форматирования используются методы format, printf.
Также в классе String есть статический метод format.
В каждом форматирующем методе обязательным аргументом является шаблон, определяющий форматирование и содержащий фиксированный текст и спецификаторы формата аргументов, далее следует перечень аргументов переменной длины.

Внутри все эти методы используют используют class java.util.Formatter, являющийся интерпретатором шаблонов форматирования. Formatter занимается тем, что парсит шаблон форматирования используя регулярные выражения и получает список объектов - спецификаторов формата, каждый из которых затем используется для форматирования соответствующего аргумента.
Для форматирования чисел могут также использоваться класс NumberFormat и его потомки (DecimalFormat).


Как осуществляется ввод и вывод из командной строки?
----------------------------------------------------
Вывод с помощью System.out, System.err - статические поля класса System.
PrintStream: print, println, printf, format, append
Ввод с помощью Scanner. 
Объект класса Console предоставляет ввод и вывод.


Что такое класс Console? Расскажите его АПИ
-------------------------------------------
java.io.Console - класс предоставляющий доступ к символьному консольному устройству, ассоциированному с текущей JVM (может отсутствовать) для чтения/печати в командной строке. Объект консоли получают методом System.console(), который может вернуть null.
Методы вывода: format(), printf().
Методы ввода: readLine() : String, readPassword() : char[].
readPassword() не показывает вводимые символы и возвращает char[], символы в котором можно перезаписать после использования.


Что такое поток данных? Data stream.
------------------------------------
DataOutputStream позволяет записывать данные примитивных типов, а также строки в utf-8, которые могут читаться потоком DataInputStream.
DataOutputStream содержит методы write() для всех примитивных типов, а также writeUTF(String), который сначала записывает в поток количество байт, которыми будет закодирована строка, а затем саму строку в кодировке UTF-8.
DataInputStream содержит методы read для всех примитивных типов, метод readUTF(), метод readFully(byte[]) для заполнения массива байтов.


Что такое поток объектов? Object stream.
----------------------------------------
Поток объектов позволяет записывать состояние объекта в поток байтов, то есть производить сериализацию объектов, а также обратный процесс - десериализацию, то есть чтение объектов из потока. При сериализации производится сохранение значений примитивных полей объекта, а также объектов ссылочных полей.
Для возможности сериализации класс должен быть помечен маркерным интерфейсом java.io.Serializable

ObjectOutputStream содержит метод writeObject(Object).
ObjectInputStream содержит метод readObject() : Object.
Эти классы также имплементируют интерфейсы DataOutput / DataInput с содержат соответствующие методы.


Что такое Path? Как он реализуется на разных ОС?
------------------------------------------------
Path представляет иерархический путь к определенному файлу или директории и состоит из последовательности имен директорий и файла разделенных символом зависящим от ОС.
java.nio.file.Path является интерфейсом. Для получения Path можно использовать статический метод get(String) класса java.nio.file.Paths. 
При получении объекта Path на Windows это будет объект класса sun.nio.fs.WindowsPath.
Path содержит методы getFileName(), getParent(), getRoot(), resolve(Path).
Функцию абстрактного пути также реализует класс java.io.File (до NIO2).


Как получить список файлов? (java.io.File)
------------------------------------------
методы возвращающие String[]: list(), list(FilenameFilter),
методы возвращающие File[]: listFiles(),  listFiles(FileFilter).

Как получить список файлов? (java.nio.file.Files) 
-------------------------------------------------
static Stream<Path> find(Path path, int maxDepth, BiPredicate<Path,​BasicFileAttributes> matcher, FileVisitOption... options) - Return a Stream that is lazily populated with Path by searching for files in a file tree rooted at a given starting file.

static Stream<Path>	list​(Path dir) - Return a lazily populated Stream, the elements of which are the entries in the directory.

static DirectoryStream<Path> newDirectoryStream​(Path dir) - возращает DirectoryStream позволяющий итерировать по всем объектам содержащимся в директории, его нужно закрывать.

static Stream<Path>	walk​(Path start, FileVisitOption... options) - возвращает Stream<Path> lazily populated во время прохождения по дереву директорий начиная со стартовой директории.

static Path	walkFileTree​(Path start, FileVisitor<? super Path> visitor) - проходит по дереву, при этом действия выполняет FileVisitor.


Как проверить, что файловая сущность является файлом или папкой?
----------------------------------------------------------------
java.io.File:
boolean	isFile() - Tests whether the file denoted by this abstract pathname is a normal file.
boolean	isDirectory() - Tests whether the file denoted by this abstract pathname is a directory.

java.nio.file.Files:
static boolean	isDirectory​(Path path, LinkOption... options) - Tests whether a file is a directory.
static boolean	isRegularFile​(Path path, LinkOption... options)	- Tests whether a file is a regular file.


Как удалить файл?
-----------------
java.io.File:
boolean	delete() - Deletes the file or directory denoted by this abstract pathname.
void	deleteOnExit() - Requests that the file or directory denoted by this abstract pathname be deleted when the virtual machine terminates.

java.nio.file.Files:
static void	delete​(Path path) - Deletes a file (or throw NoSuchFileException).
static boolean	deleteIfExists​(Path path) - Deletes a file if it exists.


Как переместить файл?
---------------------
java.nio.file.Files:
static Path	move​(Path source, Path target, CopyOption... options) - Move or rename a file to a target file.


Как управлять аттрибутами файла?
--------------------------------
java.nio.file.Files:
public static long size​(Path path) - возвращает размер в байтах.

public static FileTime getLastModifiedTime​(Path path, LinkOption... options) - возвращает время последней модификации.
public static Path setLastModifiedTime​(Path path, FileTime time) - обновляет время последней модификации.

public static UserPrincipal getOwner​(Path path, LinkOption... options)
public static Path setOwner​(Path path, UserPrincipal owner) - возвращают или устанавливают владельца файла.

static Object	getAttribute​(Path path, String attribute, LinkOption... options)
public static Path setAttribute​(Path path, String attribute, Object value, LinkOption... options) - возвращает или устанавливает значение аттрибута файла

static Set<PosixFilePermission>	getPosixFilePermissions​(Path path, LinkOption... options)
public static Path setPosixFilePermissions​(Path path, Set<PosixFilePermission> perms) - возвращает или устанавливает POSIX атрибуты доступа файла.


Как изменить время последней модификации файла?
-------------------------------------------------
Path file = ...;
long currentTime = System.currentTimeMillis();
FileTime ft = FileTime.fromMillis(currentTime);
Files.setLastModifiedTime(file, ft);


Как получить основные аттрибуты файла ? (BasicFileAttributes)
---------------------------------------
BasicFileAttributes attr = Files.readAttributes(path, BasicFileAttributes.class);

System.out.println("creationTime: " + attr.creationTime());
System.out.println("lastAccessTime: " + attr.lastAccessTime());
System.out.println("lastModifiedTime: " + attr.lastModifiedTime());

System.out.println("isDirectory: " + attr.isDirectory());
System.out.println("isOther: " + attr.isOther());
System.out.println("isRegularFile: " + attr.isRegularFile());
System.out.println("isSymbolicLink: " + attr.isSymbolicLink());
System.out.println("size: " + attr.size());


Как получить POSIX аттрибуты файла ?
------------------------------------
Path file = ...;
PosixFileAttributes attr = Files.readAttributes(file, PosixFileAttributes.class);
System.out.format("%s %s %s%n",
    attr.owner().getName(),
    attr.group().getName(),
    PosixFilePermissions.toString(attr.permissions()));


Как создать файл?
-----------------
Можно создать файл с помощью любого потока вывода (FileOutputStream, FileWriter).

java.io.File:
boolean	createNewFile() - создает новый пустой файл, если он еще не существует (атомистичная операция).

java.nio.file.Files:
static Path	createFile​(Path path, FileAttribute<?>... attrs) - создает новый пустой файл, если он еще не существует (атомистичная операция).
public static Path write​(Path path, byte[] bytes, OpenOption... options)


Как создать директорию?
-----------------------
java.io.File:
boolean	mkdir()	- создает директорию.
boolean	mkdirs() - создает директорию, а также недостающие в пути директории.

java.nio.file.Files:
static Path	createDirectory​(Path dir, FileAttribute<?>... attrs) - создает директорию.
static Path	createDirectories​(Path dir, FileAttribute<?>... attrs) - создает директорию, а также недостающие в пути директории.


Как записать в файл?
--------------------
java.io:
С использованием любого потока вывода (FileOutputStream, FileWriter).

java.nio.file.Files:
static Path	write​(Path path, byte[] bytes, OpenOption... options) - пишет байты в файл.	
static Path	write​(Path path, Iterable<? extends CharSequence> lines, Charset cs, OpenOption... options)	- пишет строки в файл.
static Path	writeString​(Path path, CharSequence csq, Charset cs, OpenOption... options) - пишет последовательность символов в файл.


Как прочитать данные из файла?
------------------------------
java.io:
С помощью любого потока ввода (FileInputStream, FileReader).

java.nio.file.Files:
static byte[]	readAllBytes​(Path path)	- читает все байты из файла.
static List<String>	readAllLines​(Path path, Charset cs) - читает все строки из файла.
static String	readString​(Path path, Charset cs) - читает содержимое как одну строку.


Tutorial: https://docs.oracle.com/javase/tutorial/essential/io/index.html
