# BigDataEducation
Data Engineer education tips

## HADOOP

**Hadoop** — свободно распространяемый набор утилит, библиотек и фреймворк для разработки и выполнения распределённых программ, работающих на кластерах из сотен и тысяч узлов.

#### Дистрибутивы Apache Hadoop:
- ClouderaCDH
- Hortonworks
- MapR

**Модули**:
- **HDFS**:
  Распределенная файловая система Hadoop для хранения файлов больших размеров с возможностью потокового доступа к информации.  
  Нерентабельно хранить файлы которые меньше блока на HDFS, (стандартные размеры 64Мб и 128Мб), таким образом если у нас будет файл 40Мб а размер блока 64Мб, то с каждого блока мы будем терять 24Мб, а если у нас таких фалов будет 1000, то потеряем уже 24Гб(а если у нас будет репликация не 1 а 2, то потеряем 48Гб).  
  Кол-во файлов ограничено размерами RAM сервера на котором запущен NameNode.

- **YARN**:
  Диспетчер ресурсов в кластерах Apache Hadoop и Spark, отвечает за выделение ресурсов распределенным приложениям

- **MapReduce**:
  Фреймворк для написания приложений, которые обрабатывают большие объемы данных параллельно.
  Работа MapReduce заключается в разбивке входного набора данных на независимые блоки, которые обрабатываются параллельно.

- **Common**:
  Предоставляет инструменты, необходимые в операционных системах для чтения данных, хранящихся в файловой системе Hadoop.

-------------------------------------------------------------------
**Архитектура**:
- **NameNode(управляющий узел)** — отвечает за открытие и закрытие файлов, создание и удаление каталогов, управление доступом со стороны внешних клиентов.

- **Secondary NameNode** — выполняет периодические checkpoints пространства имен и помогает поддерживать размер файла, содержащего журнал изменений HDFS, в определенных пределах в NameNode; {fsimage}

- **DataNode(сервер данных)** — это стандартный сервер, на котором хранятся данные. Он отвечает за запись и чтение данных и выполняет команды NameNode.

- **Сlient(пользователь)** — которому предоставляют доступ к файловой системе.

![HDFS architecture](/image/hdfs_arch.png)
-------------------------------------------------------------------
VM - процессор, изолируется полностью, полностью готовая система
Контейнер на ядре / для изоляции использует ОС, внутри контейнера 1 приложение

LZO — алгоритм сжатия данных

Iac - подход для управления ЦОД через конфигурационные файлы, а не через ручное редактирование конфигураций

-------------------------------------------------------------------
## Data Warehouse
Представляет собой хранилище разных данных, которые уже отсортированы и преобразованы.  
Хранилище, предназначенное для сбора и аналитической обработки исторических данных организации.
В DWH данные аккумулируют и очищают, формируя единый источник.

#### DWH vs СУБД
DWH предназначено для анализа данных, которые поступают в него с определённой периодичностью — например, ежечасно или ежедневно. Оно разворачивается поверх СУБД и способно быстро обрабатывать большие массивы данных
DWH фактически — инструмент для комплексного анализа данных из множества источников: по определенным заданным параметрам

СУБД в основном предназначены не для аналитики, а для повседневной работы. Информация в них обновляется в реальном времени.

## Data Lake 
— это репозиторий, в котором хранятся огромные объемы необработанных данных в исходном формате до тех пор, пока они не потребуются.
Каждому элементу данных в озере присваивается уникальный идентификатор, и он помечается набором расширенных тегов метаданных.
Когда возникает деловой вопрос, пользователь может найти и извлечь из озера нужные ему файлы.

### ETL и ELT
Data Warehouse использует метод ETL – Extract, Transform и Load, то есть дословно переводится как «извлечение», «преобразование» и «загрузка».
Data Lake использует ELT  — Extract, Load и Transform, то есть сначала идет «загрузка», а только потом «преобразование».

## Типы данных
**Структурированные** — фиксированный формат, известен заранее: SQL  
**Полуструктурированные** — не вписывается в жесткую структуру, которую требуют реляционные базы данных: XML, JSON, фото с тегами  
**Неструктурированные** — информация представленная в необработанном виде

-------------------------------------------------------------------
**Схема на чтение** — datalake, можно писать что угодно, но когда читаем нужно применять схему, ELT  
**Схема на запись** — datawarehouse(реляционные БД), когда неправильная схема - будет ошибка, ETL

-------------------------------------------------------------------
## Форматы файлов
https://www.bigdataschool.ru/blog/row-vs-column-file-format-big-data.html

Cтроковые(линейные) форматы (AVRO, Sequence)
- Скорость чтения - (будет считана вся строка)
- Не являются строго типизированными
- MapReduce + данные могут быть использованы независимо друг от друга.
- Степень сжатия ниже -
- Восстановление +

Колоночно-ориентированных форматы (Parquet, ORC)
- Скорость чтения + (можно пропускать ненужные столбцы)
- Занимает больше места в оперативной памяти - (поскольку, чтобы получить столбец из нескольких строк, кэшируется каждая строка)
- Степень сжатия выше +
- Восстановление - (нет точек синхронизации)

Примеры использования:
- Линейный формат AVRO обеспечивает высокую скорость записи информации, и потому отлично подходит обработки потоков Big Data в Apache Kafka
- Колоночный формат быстрее считывают данные из файла, быстрее считывают данные из файла поэтому подходит к СУБД на основе Apache Hadoop

Avro по сравнению с Parquet:  

Avro — формат хранения по строкам, тогда как Parquet хранит данные по столбцам.  
Parquet лучше подходит для аналитических запросов, то есть операции чтения и запрос данных гораздо эффективнее, чем запись.  
Операции записи в Avro выполняются эффективнее, чем в Parquet.  
Avro более зрело работает с эволюцией схем. Parquet поддерживает только добавление схемы, а в Avro реализована многофункциональная эволюция, то есть добавление или изменение столбцов.  
Parquet идеально подходит для запроса подмножества столбцов в многоколоночной таблице. Avro подходит для операций ETL, где мы запрашиваем все столбцы.

ORC по сравнению с Parquet: 

Parquet лучше хранит вложенные данные.  
ORC лучше приспособлен к проталкиванию предикатов (predicate pushdown).  
ORC поддерживает свойства ACID.  
ORC лучше сжимает данные.

-------------------------------------------------------------------
## Docker Modules

- **Docker daemon** — осуществляется все взаимодействие с контейнерами: создание и удаление, запуск и остановка
- **Docker client** — интерфейс командной строки для управления docker daemon. Мы пользуемся этим клиентом, когда создаем и разворачиваем контейнеры, а клиент отправляет эти запросы в docker daemon.
- **Docker image(образ)** — неизменяемый файл, из которого разворачиваются контейнеры. Приложения упаковываются именно в образы, из которых потом уже создаются контейнеры.
- **Дистрибутив для установки ОС** — это Docker image, а установленная и работающая ОС — это Docker container
- **Docker container** — уже развернутое из образа и работающее приложение.
- **Docker Registry** — репозиторий с докер-образами. Разработчики будут размещать там образы, которые будут использоваться всей компанией.
- **Dockerfile** — это инструкция для сборки образа. Это простой текстовый файл, содержащий по одной команде в каждой строке. В нем указываются все программы, зависимости и образы, которые нужны для разворачивания образа.

-------------------------------------------------------------------
## Нормализация/Денормализация

1НФ:
- В каждой клетке таблицы только одно значение
- Не должно быть повторяющихся строк

2НФ:
- 1НФ
- Есть первичный ключ
- Все атрибуты зависят от первичного ключа, а не от какой-то ее части

3НФ:
- 2НФ
- Все атрибуты зависят от первичного ключа, но не от других атрибутов

Денормализация — поместить избыточные данные туда, где они смогут принести максимальную пользу.  
Логика в том, чтобы снизить время исполнения определенных запросов через упрощение доступа к данным или через создание таблиц с результатами отчетов, построенных на основании исходных данных.

dimensional modeling!! примеры

-------------------------------------------------------------------
## Spark

**Spark** - фреймворк с открытым исходным кодом, для распределённой обработки неструктурированных и слабоструктурированных данных.

**Spark** из-за поддержки системой вычислений в памяти в десятки раз производительнее Hadoop.
Hadoop после каждый итерации записывает в HDFS, Spark - в RAM.
Так же Hadoop всегда используется map-reduce, даже если нужно сделать несколько reduce подряд.

#### Устранение неполадок Spark: https://docs.qubole.com/en/latest/troubleshooting-guide/spark-ts/troubleshoot-spark.html

#### Spark cluster architecture:

- **Driver** – мастер-процесс, который преобразует программы в задачи и планирует их для исполнителей с помощью планировщика задач (Task Scheduler)  
- **Cluster Manager** – ядро фреймворка, которое позволяет запускать исполнители, а иногда и драйверы. Именно здесь планировщик (Scheduler) планирует действия и задания приложения в режиме FIFO, т.е. прямой очереди.  
- **Slave Processes** – сущности, на которых выполняется отдельная задача из задания. Запущенные исполнители работают до завершения жизненного цикла приложения, а в случае отказа перехватывают работу друг друга, чтобы продолжить выполнение задания.  
- **RDD** – это распределенная коллекция неизменяемых наборов данных на разных узлах кластера, разделенная на один или несколько разделов (partition), чтобы добиться параллелизма внутри приложения за счет локальности данных. Преобразования повторного разделения (repartition) или объединения (coalesce) позволяют сохранить количество разделов.  
- **DAG (Directed Acyclic Graph)** – направленный ациклический граф операторов, который генерирует фреймворк, когда пользовательский код введен в консоль. При запуске действия с RDD, фреймворк отправляет DAG в планировщик графов (DAGScheduler), где графы операторов разделяются на этапы задачи (stages). Каждый этап может содержать задания, основанные на нескольких разделах входных данных. DAGScheduler объединяет эти графы операторов в конвейер (pipeline). Например, граф оператора Map составляет граф для одного этапа, который переходит в Планировщик заданий в диспетчере кластеров для их выполнения. Далее эта задача исполняется worker’ом или executor’ом на ведомом устройстве (slave).

### Spark context and Spark session

Точка входа позволяет обмениваться данными с источниками данных и выполнять определенные операции, такие как чтение и запись данных.  
**SparkContext**, **SQLContext** и **HiveContext** точки входа начиная со Spark 1.x  
**SparkSession** точка входа начиная со Spark 2.x, объединяет все прошлые  

-------------------------------------------------------------------

Ленивая оценка - Apache Spark откладывает оценку до тех пор, пока это не станет абсолютно необходимым.

Это один из ключевых факторов, влияющих на его скорость. Для преобразований Spark добавляет их в вычислительную группу DAG, и только когда драйвер запрашивает некоторые данные, эта группа DAG фактически выполняется.


#### Преобразования
**Узкие преобразования (Narrow transformations)** — это когда нет перемещения данных между разделами. Преобразование применяется к данным каждого раздела RDD, и создается новый RDD с тем же числом разделов. Например, filter – это узкое преобразование, потому что фильтрация применяется к данным каждого раздела, а полученные данные представляют раздел во вновь созданном RDD.  
**Широкие преобразования (Wide transformations)** - требуют перемещения данных между разделами или так называемого перемешивания. Данные перемещаются с целью создания нового RDD. Например, sortBy сортирует данные на основе определенного столбца и возвращает новый RDD.  

#### Spark join strategy
- Inner Join – выводит только совпавшие по условию соединения объединенные записи из входных наборов данных  
- Outer Join – в дополнение к сопоставленным соединенным записям также выводит несоответствующие записи. Outer Join дополнительно делят на левое, правое и полное, в зависимости от набора входных данных для вывода несовпадающих записей.  
- Semi Join – выводит отдельную запись, принадлежащую только одному из двух входных наборов данных в совпавшем или в несоответствующем экземпляре. Если запись, принадлежащая одному из входных наборов данных, выводится в несоответствующем экземпляре  
- Cross Join – выводит все соединенные записи, которые возможны путем соединения каждой записи из одного набора входных данных с каждой записью другого набора входных данных.

https://www.bigdataschool.ru/wp-content/uploads/2020/12/sqljoin1.png

-------------------------------------------------------------------
#### AQE - релиз Spark 3.0
Основная идея AQE состоит в том, чтобы сделать план выполнения не окончательным и перепроверять статус после каждого этапа. Таким образом, план выполнения разбивается на новые абстракции «этапов запроса», разделенных этапами.

Типовые правила применяемые к логическому плану запроса:  
**predicate pushdown** - позволяет оптимизировать запросы Spark SQL, фильтруя данные в запросе к СУБД и уменьшая количество извлекаемых записей. По умолчанию Spark Dataset API автоматически передает действительные WHERE-условия в базу данных.  
**partition pruning** - убирает из рассмотрения партиции, не удовлетворяющие фильтру.

#### .repartition() and .coalesce()
Методы разделяющие DataFrame на части, repartition на примерно одинаковые, так же можешь уменьшать или увеличивать число разделов  
сoalesce - только уменьшать

-------------------------------------------------------------------
#### .cache() vs .persist()
Для кэширования данных в Apache Spark применяется методы cache или persist. Первый метод кеширует в оперативной памяти, а второй на выбор в зависимости от переданного аргумента.  
Хорошее правило: определите DataFrame, который вы будете повторно использовать в своем приложении Spark, а затем произведите кэширование.

**Зачем?**  
- Повторное использование данных.  
- Аналог контрольной точки.  
- Читать с локального диска быстрее чем из удаленного источника.

persist аргументы:
- DISK_ONLY: данные сохраняются на диске в сериализованном формате
- MEMORY_ONLY: данные сохраняются в оперативной памяти в десериализованном формате
- MEMORY_AND_DISK: данные сохраняются в оперативной памяти, и если памяти недостаточно, вытесненные блоки будут сохранены на диске
- OFF_HEAP: данные сохраняются в памяти вне кучи

MEMORY_ONLY_SER - Использование сериализованного формата увеличит время обработки, но уменьшит объем памяти.

DISK_ONLY_2, MEMORY_AND_DISK_2 - Использование репликации

-------------------------------------------------------------------
#### RDD vs DF vs DS
- RDD — представление данных в объектном формате  
RDD — это набор объектов Java или Scala, представляющих данные.  
Отказоустойчив — если узел, удерживающий раздел, выходит из строя, другой узел берет данные.  
- DataFrame — это распределенная коллекция данных, организованных в именованные столбцы. Концептуально она равна таблице в реляционной базе данных.  
- DataSet — type-safe/ быстрее RDD, но медленнее DataFrame/доступен только в Scala/Java


- RDD — структурированными\неструктурированными данными\схему нужно указать пользователю  
- DataFrame — структурированными\полуструктурированными\схема определяется автоматически  
- DataSet — структурированные\неструктурированные данные\схема определяется 

-------------------------------------------------------------------
## Spark memory model

### Spark Catalyst 
Встроенный оптимизатор структурированных запросов в Spark SQL.

1. Создает дерево для неразрешенного(нет информации о типах данных) логического плана.  
2. Начинает применять к нему правила, пока не разрешит все ссылки на атрибуты и отношения.  
3. Применяет все правила оптимизации к логическому плану, оптимизирует затраты.  
4. Генерирует несколько физических планов на основе логического плана для выбора наиболее эффективного.  
5. Генерация кода.

### Tungsten

Используется в DataSet, DataFrame  
Благодаря инструменту Tungsten не требуется использовать сериализацию Java для кодирования данных.  
Tungsten реализует собственный формат сериализации, называемого небезопасной строкой (UnsafeRow). Он быстрее и компактнее Kryo и сериализация Java.

-------------------------------------------------------------------
## Spark streaming

**Spark Watermark** — это пороговое значение, указывающее, как долго система ожидает поздних событий. Если поступающее событие находится внутри водяного знака, оно используется для обновления запроса. В противном случае, если он старше водяного знака, он будет удален и не будет дополнительно обработан потоковым движком.

**Spark Checkpointing** — восстановление системы хранения после сбоев:
- Контрольные точки метаданных: используются для восстановления узла драйвера потокового приложения после сбоя.
  Он включает в себя конфигурации, используемые для создания приложения, операции DStream и неполные пакеты.
- Контрольные точки данных: При потоковой передаче с отслеживанием состояния созданные RDD сохраняются в хранилище, например HDFS.
  Это тот случай, когда предстоящий RDD для некоторых преобразований зависит от предыдущих RDD. В этом случае длина цепочки зависимостей со временем продолжает увеличиваться.
  Таким образом, чтобы избежать увеличения времени восстановления, промежуточные RDD пероидно блокируются в некотором надежном хранилище.

**KStream / KTable**  
KTable разделяет данные между всеми запущенными экземплярами Kafka Streams, в то время как GlobalKTable содержит полную копию всех данных в каждом экземпляре.
Недостатком GlobalKTable является то, что ей, очевидно, требуется больше памяти. Преимущество заключается в том, что вы можете выполнить соединение KStream-GlobalKTable с неключевым атрибутом из потока.

**ksqlDB** можно рассматривать в качестве своего рода надстройки над Kafka Streams,
поскольку этот инструмент позволяет преобразовать SQL-запросы для потоковой аналитики больших данных, в распределенные приложения.

Архитектура:  
- Engine – обрабатывает SQL-операторы и запросы. Пользователю просто определяет логику своего приложения, записывая SQL-операторы, а движок сам создает и
запускает приложение на доступных серверах ksqlDB, работая на каждом экземпляре сервера. Движок, реализованный реализован в классе KsqlEngine.java,
сам анализирует пользовательские операторы SQL и строит соответствующие топологии Kafka Streams.  
- REST-интерфейс для клиентского доступа к движку позволяет взаимодействовать с механизмом ksqlDB из интерфейса командной строки,
Confluent Control Center или любого другого клиента REST. REST-сервер ksqlDB реализован в классе KsqlRestApplication.java.
- интерфейс командной строки (CLI) – консоль для взаимодействия с экземплярами сервера и разработки потоковых приложений.
- Пользовательский интерфейс ksqlDB позволяет разрабатывать приложения ksqlDB в Confluent Control Center и Confluent Cloud.

#### Output modes:

**Append Output Mode** .outputMode("append")  
Режим вывода, в котором в приемник будут записаны только новые строки в потоковом наборе данных.

**Complete Output Mode** .outputMode("complete")  
Режим вывода, в котором все строки в потоковом наборе данных будут записываться в приемник каждый раз, когда будут какие-то обновления.

**Update Output Mode** .outputMode("update")  
Режим вывода, в котором только строки, которые были обновлены в потоковом наборе данных, будут записываться в приемник каждый раз, когда будут какие-то обновления.

-------------------------------------------------------------------
## Spark ML

Типы обучения:
- Контролируемое обучение(Supervised learning)
- Обучение без присмотра(Unsupervised learning)
- Полуконтролируемое обучение(Semi-supervised learning)
- Обучение с подкреплением(Reinforcement learning) - сообщают машине, какой шаг правильный, а какой нет

Общие шаги типичного решения для машинного обучения:
- Разработка функций
- Обучение модели
- Оценка модели - тут проверяют точность сформированной модели и отсутствие переобученности

-------------------------------------------------------------------
## Databricks
Фреймворк Databricks поставляет среду для хранения и обработки данных, Databricks позволяет создавать код, используя множество языков, в одном процессе.

Платформа Databricks состоит из нескольких слоев:

#### Delta Lake
- Совместимость с Spark API
- Данные хранятся в столбцовом формате Apache Parquet
- Схема данных
- Эволюция схемы

#### Delta Engine
Оптимизированная система обработки запросов для работы с данными, хранящимися в Delta Lake.

-------------------------------------------------------------------
## KAFKA

**Apache Kafka** - это распределенная платформа потоковой передачи событий с открытым исходным кодом

**Admin Client** - полезен, когда требуется выполнять некоторые административные команды из клиентского приложения без использования инструментов CLI и GUI для управления Kafka.

maintein ability

!!! как именно консюмер читает сообщения


consumer group - партиции в топиках делятся между потребителями в группе

compacted topic - хранится по одному значению в партиции, значения удаляются по ключу и оставляют самое позднее с таким ключом.

Балансировка нагрузки kafka\Consumer Load Balancing

### Оптимизация

-------------------------------------------------------------------
**Kafka compression** - сжатие данных с помощью различных кодеков – gzip/snappy
Минус оптимизации - увеличение загрузки ЦП на стороне потребителя за счет распаковки сжатых данных.

**Kafka Producer Batch** - объединение нескольких сообщений в пакет для отправки брокеру

-------------------------------------------------------------------
Kafka acks - указывает, сколько подтверждений должен получить продюсер, чтобы запись считалась доставленной брокеру

- 0, когда producer вообще не ждет никакого подтверждения, а сообщение считается в любом случае отправленным.
- 1, когда отправленное сообщение записывается в локальный журнал одного брокера в кластере Кафка (лидер, leader), не ожидая полного подтверждения от всех остальных серверов
- -1 или all, когда producer ждет полной репликации сообщения по всем серверам кластера, что обеспечивает надежную защиту от потери данных, но увеличивает задержку (latency) и снижает пропускную способность.

Consumer Delivery Semantics:

- хотя бы 1 раз (at least once), когда отправитель сообщения получает подтверждение от брокера Kafka при значении параметра acks=all, что гарантирует однократную запись сообщения в топик Kafka. Но если отправитель не получил подтверждения по истечении определенного времени или получил ошибку, он может повторить отправку. При этом сообщение может быть дублировано, если брокер дал сбой непосредственно перед отправкой подтверждения, но после успешной записи сообщения в топик Kafka.
- не более 1-го раза (at most once), когда отправитель не повторяет отправку сообщения при отсутствии подтверждения или в случае ошибки. При этом возможна ситуация, что сообщение не записано в топик Кафка и не получено потребителем. На практике в большинстве случаев сообщения будут доставляться, но иногда возможна потеря данных.
- строго однократно (exactly once), когда даже при повторной попытке отправителя отправить сообщение, оно доставляется строго один раз. В случае ошибки, заставляющей отправителя повторить попытку, сообщение будет однократно записано в логе брокера Kafka. Это избавляет от дублирования или потери данных из-за ошибок на стороне producer’a или брокера Кафка. Чтобы включить эту функцию для каждого раздела следует задать свойство идемпотентности в настройках отправителя idempotence=true. Напомним, идемпотентной считается операций, которая при многократном выполнении даёт тот же результат, что и при однократном.

commitSync - блокирует поток до тех пор, пока не будет встречен либо коммит успешно, либо неустранимая ошибка  
commitAsync - встреченные ошибки либо передаются в callback (если он предусмотрен) или отбрасываются

Kafka connect:
- worker — инстанс/сервер Kafka Connect
- connector — Java class + пользовательские настройки
- task — один процесс connector'a

-------------------------------------------------------------------

TF/IDF

Elastic X-Pack

Кибана

logstash

elasticsearch/Elasticsearch plugin
