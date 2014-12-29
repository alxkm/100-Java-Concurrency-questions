#Общие вопросы

**В чем разница между потоком и процессом?**

Процесс является более высокоуровневой абстракцией ОС для выполнения программы, чем поток. Он, в свою очередь, может запускать в себе несколько потоков. Процесс всегда имеет хотя бы один (главный) поток.

**Что такое кооперативная многозадачность и она ли в Java. Если да, то какие преимущества. Если нет, то какая тогда в Java?**

Кооперативная многозадачность - это техника конкурентного выполнения программ, в которой обязанность передачи управления перекладывается на саму программу. 
Преимущество корпоративной многозадачность заключается в большей надежности и предопределенности выполнения программ, так как при таком подходе программа сама контролирует последовательность выполнения задач.
В Java вытесняющая многозадачность.

**Сравните кооперативную и вытесняющую многозадачности**

При вытесняющей многозадачности материнская плата имеет таймер, который генерирует прерывания через определенные промежутки времени(~ 1мс). По этому таймеру операционная система передает управление планировщику. Планировщик выбирает задачу из очереди и передает управление этой задачи. Задача выполняется определенное количество времени и безусловно прерывается.
При вытесняющей многозадачности программа после завершения работы уведомляет операционную систему, что она завершила работу и можно передать управление другой программе.

Преимущества кооперативной многозадачности:

- Полный контроль за выполнением программы. То есть программисты такой системы знают, что программа не будет прервана посередине выполнения важной задачи. Это критично для систем с жесткими гарантиями по времени выполнения.
- Программа получает в свое распоряжение все системные ресурсы.
- Реализация операционной системы с кооперативной многозадачностью проще, чем с вытесняющей.

Преимущества вытесняющей многозадачности:

- Программа, написанная с ошибками, не заберет себе все ресурсы сервера. Планировщик принудительно прервет ее выполнение и передаст управление другому процессу.
- Возможность эмуляции параллельной работы нескольких программ. Система не "зависает", пока одна из программ выполнят свою задачу.
То есть задача, потребляющая большое количество процессорного времени, не заберет себе все ресурсы и не заблокирует систему от выполнения другой активности.
- Программирование на системах с вытесняющей многозадачностью проще, так как программисту не нужно думать о том, как и когда отдавать управление операционной системе. Она сама заботится об этом.

Системы общего назначения обычно используют вытесняющую многозадачность. В то же время, системы с жестким временем выполнения (медицинское, автомобильное, аэрокосмическое оборудование) полагаются на кооперативную многозадачность.

**Что такое «зеленый потоки» и они ли в Java (в HotSpot JVM 7)?**

"Зеленые потоки" - это потоки, которыми управляет виртуальная машина, а не операционная система. Они дают возможность эмулировать многопоточность внутри процесса без переключения контекста между пользательским режимом и режимом ядра.
Преимущество зеленых потоков заключается в том, что они легче, чем системные потоки (не нужно сохранять стек на каждый поток и ходить в режим ядра для переключения). Программист может создавать тысячи зеленых потоков, в то время как у системных потоков есть практическое ограничение на их количество. Это может быть полезно в случае, если задача не ограничена процессорным временем, но выполняет частый ввод-вывод. В этом случае затраты на переключение между потоками будут намного меньше, при этом программист имеет абстракцию последовательного выполнения кода.

Нет, в Java потоки маппятся 1 в 1 на системные потоки.

**Когда началась «Multicore Era»?**

В начале 2000-х с появлением процессорорв серии POWER от IBM. Затем в 2005 появились Pentium D и AMD Athlon 64 X2.
Связано с тем, что частота процессоров больше не могла расти из-за физических ограничений и технологических проблем, возникающих при увеличинии плотности транзисторов на поверхности кристалла. Классическая статья - [The Free Lunch Is Over](http://www.gotw.ca/publications/concurrency-ddj.htm)

**Что такое — Планировщик потоков? Предположите алгоритм работы**

Планировщик потоков - программа на уровне операционной системы, которой по прерыванию управление через определенные промежутки времени. Ее задача - распределять процессорное время между процессами, выполняющимся в системе.

* Заводится кольцевой буфер;
* Когда процесс начинает работу, он добавляется в буфер;
* Когда заканичивает - удаляется;
* Планировщик выбирает процесс с головы буфера;
* Голова буфера сдвигается к следующему процессу;
* Процесс выполняется свой квант времени;
* Текущий процесс прерывается;
* Управление передается процессу в голове буфера

**Какие выигрыши может дать многопоточность на одноядерной машине?**

* Возможность "практически одновременно" выполнять несколько задач;
* Лучшая "отзывчивость" систем, для которых более важен быстрый ответ, а не общее количество выполненной работы;
* Более быстрое выполнение задач, которые не ограничены процессорным временем.
Скажем, у нас есть n задач, которые выполняют I/O (чтение из сети) и они не связаны между собой. В этом случае мы можем их параллельно запустить и обрабатывать результаты каждой задачи, только когда они будут готовы. Если бы эти задачи выполнялись параллельно, то мы бы тратили процессорное время на задержки сети. 

# «Железо»

**Что такое Flynn’s taxonomy, SISD/MISD/SIMD/MIMD? К какому классу относятся CPU? GPU?**

Таксономия Флинна - это классификация вычислительных архитектур по типу параллельных инструкций и потоков данных.

* SISD - вычислительные системы с одним потоком данных и одним потоком инструкций (типичиная архитектура для одноядерных процессоров).
* MISD - вычислительные системы с одним потоком данных и несколькльами потоками инструкций (довольно редкая архитектура). 
* SIMD - вычислительные системы с несколькими потоками данных и одним потоком инструкций (векторные процессоры, GPU).
* MIMD - вычислительные системы с несколькими потоками данных и несколькими потоками инструкций (распределенные системы).

Одноядерные процессоры относятся к SISD системам, многоядерные процессоры к SIMD или MIMD. GPU является SIMD системой, потому что использует векторные инструкции для операций над мультимедиа-данными.

**Расскажите про иерархию кэшей L1/L2/L3? Что вызвало ее появление?**

L1/L2/L3 - кэши данных, которыми оперирует процессор.

* L1 - кэш на ядре процессора с скоростью доступа порядка 1 нс. Размер обычно около 32 КБ.
* L2 - более крупный и менее быстрый кэш. Скорость доступа порядка 5 нс. Размер порядка 256 КБ.
* L3 - кэш на процессоре, общий для всех ядер. Скорость доступа порядка 20 нс. Может быть достаточно большим (8-32 MB). 

Появление кэшей вызывало тот факт, что скорость работы процессора начала становится намного больше скорости доступа к памяти. Поэтому для того, чтобы избежать огромных задержек по доступу к данным из основной памяти (порядка 60 нс), производителями процессоров были добавлены кэши на процессорах для быстрого выполнения операций над "горячими" данными. 

**Что такое Memory wall?**

Memory wall - это термин, описавающий ограничение производительности вычислительных систем скоростью доступа к памяти. Несмотря на то, что частота процессоров в 70-90-е годы увеличивалась в 2 раза каждые 2 года, скорость доступа к памяти не увеличивалсь с такой же степенью. В итоге производительность системы в целом упиралась в производительности памяти, так как процессоры не могли работать на своей полной вычислительной мощности.

**Что такое Memory Hierarchy**

Memory Hierarchy - термин для описания производительности систем хранения данных. Чем ниже уровень иерархии, тем дешевле цена системы хранения и больше время доступа к ней. Обычно выделяют 4 уровня:

* Регистры и кэши процессора (скорость доступа порядка 100-500 Гб/c)
* Оперативная память (скорость доступа порядка 1-10 Гб/c)
* Диски (скорость доступа порядка 100-500 Мб/c)
* Третичные память (скорость доступа порядка 10-100 Мб/c)

**Что такое Cache line? В виде каких эффектов проявляется?**

Cache line - блок данных(обычно 64 байт), в котором передаются данные между процессором и оперативной памятью. Когда процессору нужно прочитать данные по конкретному адресу из опертивной памяти, он вместо 1 байта читает сразу блок данных и кладет этот блок в кэш. 
Такая оптимизация хорошо работает в случае, если данные, над которыми работает процессор, обладают хорошей локальностью. Тогда при следующем чтении данные уже будут в кэше и процессору не нужно будет делать дорогой запрос в оперативную память.

**Что такое False sharing? Плохо это или хорошо? Как с этим бороться?**

False sharing - эффект при котором данные, не связанные с друг другом, попадают в одну кэш-линию. В итоге когда изменяется одна из частей данных в кэш-линии, вся линия инвалидируется. Это плохой эффект, так как он может вызывать трешинг (thrashing) - постоянную подгрузку и инвалидацую данных их кэша. Например, часто читаемые данные могут попать на одну линию с часто записываемыми. При каждой записи линия будет инвалидироваться из кэша вместе с часто читаемыми данными, хотя сами данные не менялись.

Бороться можно с помощью техники паддинга (padding). Часто читаемые данные выравниваются по модулю длины кэш-линии с помощью фиктивных байтов. В итоге исключается вариант, когда вместе с ними на кэш-линию попадают "случайные" данные.

**Что такое Memory padding?**

Memory padding - это техника выравнивая структуры данных по границам читаемового процессором размера слова. 
Она позволяет быть уверенным, что данные всегда попадут на одну кэш-линию и займут ее экслюзивно. Это позволяет избежать трешинга кэша, в случае, если на линию попадают не соотносящиеся друг с другом данные.

**Что такое Cache pollution? Плохо это или хорошо? Как с этим бороться?**

Cache pollution - эффект при котором, происходит трешинг кэшей из-за того, что активные данные не имеют локальности. Если данные на находятся на одной кэш-линии, то при чередующем обращении к 1-му и 2-му набору данных, кэш будет постоянно инвалидировать и подгружать данные.

Это плохой эффект, так как он может привести к заметному падению производительности приложения без видимой для программиста причины. 

Бороться можно изменением подхода к обработке данных. Идея заключается в том, чтобы в один период времени работать только с "горячими" данными в кэше и начинать работу со следующим набором данных только после того, как первый больше не нужен.

**Что такое Cache miss? Плохо это или хорошо? Как с этим бороться?**

Cache miss - ситуация, когда запрашиваемого блока данных нет в кэше. В общем случае, это не является плохим эффектом, так если данные запрашиваются первый раз, то они все равно должны быть загружены в кэш. Процессоры часто спекулируют о паттернах доступа данных и вместе с запрашиваемым блоком данных кэшируют больший блок данных, полагаясь на локальность данных. Поэтому для близко лежащих данных промахов не будет. Проблема возникает, когда промахов становится очень много. Это значит, что данные не локальны и кэш не может эффективно предпогружать данные.
Бороться с промахами можно с помощью изменения расположения данных в памяти (большая локальность), изменением патернов доступа к данным (избегать трешинга), тюнингом размера кэша.

**В чем разница между SMP и NUMA? Как это сказывается на программах?**

SMP-системы имеют одну большую память, которая распределяется между процессорами. Процессоры общаются с памятью через глобальную шину данных. В NUMA-системах, в свою очередь, для каждого процессора есть своя локальная память и локальная шина. Это сильно снижает трафик на шинах в случае если процессоры работают над независимыми данными. 
В операционной системе должна быть поддержка NUMA-систем (специальный аллокатор памяти, привязка программ к процессору), иначе производительность системы будет намного хуже, чем SMP из-за отсуствия локальности данных и, следовательно, большой цены (~100 нс) последующей загрузки данных не из локальной памяти для процессора. 

**В чем разница между Multicore и Multisocket?**

Multicore - это один процессор с несколькими ядрами.
Multisocket - это несколько одноядерных процессоров.

Multisocket системы дороже, чем Multicore (нужен отдельный сокет на каждый процессор. А каждый процессор в свою очередь является чипом). Они лучше работают, если процессоры сильно не зависимы от друг друга (обрабатывают разный набор данных). Тогда можно избежать трешинга L3-кэшей, так как они у процессоров раздельные. 

В общем случае Multicore системы работают лучше, так как программы обычно не настолько требовательны к ресурсам, чтобы потреблять всю вычислительную мощность ядра/процессора. При этом засчет L3 кэша переключение программ с ядро на ядро в multicore-процессорах происходит быстрее.

**Что такое Thread affinity? Можно ли сделать в Java?**

Thread affinity - это возможность привязать выполнение потока к определенному ядру. Это является оптимизацией производительности в мультипроцессорных системах. Ее цель - увеличить процент "попадания" в кэш при доступе к данным. Смысл оптимизации в извлечении пользы из наблюдения о том, что большинство потоков обычно работают с одним и тем же набором данных. То есть после того как поток прервется планировщиком и заново получит квант времени, ему лучше выполнится на том же ядре, так как скорее всего оно уже будет содержать нужные данные в своем кэше.

Из Java напрямую сделать нельзя, но есть возможность сделать через JNA/JNI, если известна целевая платформа.

**Что такое Inter Thread Parallelism? Task Parallelism?**

Честно говоря, термины мне не известны. Возможно, имеется ввиду hyper threading? Это технология симуляции нескольких процессоров для операционной системы засчет дублирования вычислительных регистров. Операционная система планирует 2 потока на "виртуальные" процессоры, и кладет данные в регистры. Процессор же в свою очередь выполняет инструкции последовательно. Идея в том, чтобы уменьшить цену переключения потоков, передавая сразу 2 потока инструкций на процессор. Процессор, имея больше информации о потоке инструкций, может делать больше спекулятивных оптимизаций.

**Что такое Branch Prediction?**

Branch Prediction - это предсказание процессором перехода на определенную ветку выполнения кода. Так как современные процессоры работают конвеером и переходят к следующей инструкции до ее выполнения, то очень неэффективно ждать, пока определится результат условия.
Поэтому процессоры спекулятивно выполняют одну из веток и только потом определяют, была догадка правильной или нет. В случае, если процессор не угадывает ветку выполнения, то он возвращается назад и начинает конвеер сначала.

**Что такое Speculative Execution?**

Speculative Execution - это оптимизация, при которой выполняется некоторая часть кода до проверки необходимости ее выполнения. Идея заключается в наблюдении о том, что многие условия на практике перенаправляют выполнению на одну ветку, и очень редко на другую.
Пример - проверка кода ошибки. В большинстве случаев операция завершится успешно. Если процессор не делал бы спекулятивное выполнение, то он бы вынужден был всегда ждать выполнения последней команды перед условием. А так он может сразу переходить к ветке с успешным выполнением, не дожидаясь конца выполнения операции. В редком случае ошибки процессор просто вернется к условию и выполнит ветку обработки ошибки.

**Сравните с точки зрения программиста — машину с четырехядерным процессором и двумя двухядерными процессорами.**

В общем, для программиста нет никакой разницы. ОС в обоих случаях будет показывать 4 доступных процессора.
Единственный случай - это если имеется 2 большие независимые программы. Тогда имеет смысл запустить их на разных физических процессорах, чтобы избежать трешинга L1-L2 кэша.

**Напишите программу, которая демонстрирует False sharing**

См. [false-sharing](https://github.com/arteam/100-Java-Concurrency-questions/tree/master/false-sharing)

Бенчмарк измеряет одновременное чтение и запись полей объекта в памяти. По предположению в отстуствие паддинга должен проявляться эффект false sharing. То есть записи в поле y должны делать кэш-линию инвалидной и вследствии чего поле x будет читаться из памяти, а не из кэша.
 
Из результатов бенчмарка видно, что в случае расположения полей объекта на одной кэш-линии пропускная способность чтений в 3 раза хуже, чем в случае, когда JVM выравнивает поле x по линии кэша.

````
c.g.a.b.FalseSharingBenchmark.contended:read      thrpt   10   60.870 ±  5.394  ops/us
c.g.a.b.FalseSharingBenchmark.falseSharing:read   thrpt   10   19.164 ±  5.159  ops/us
````

**Напишите программу, которая демонстрирует размер кэша L1**

См. [l1-cache-size](https://github.com/arteam/100-Java-Concurrency-questions/blob/master/cache-size/src/main/java/com/github/arteam/L1CacheSize.java)

К сожалению, получить размер кэша опытным путём не получилось...

**Напишите программу, которая демонстрирует размер кэша L2**

См. [l2-cache-size](https://github.com/arteam/100-Java-Concurrency-questions/blob/master/cache-size/src/main/java/com/github/arteam/L2CacheSize.java)

Опять же, опытным путём найти значение не получилось.

# Java Memory Model

**Что такое частичный порядок?**

Частичный порядок - это математическое концепция, которая описывает отношение, которое обладает свойствами рефлексивности, антисимметричности и транзитивности. 

* Рефлексивность означает, что отношение, примененное к одинаковым аргументам, является истинным.
* Антисимметричность означает, что отношение не симметрично. То есть отношение, примененное к неодинаковым аргументам, поменняными местами, будет ложным.
* Транзитивность означает, что если отношение истинно для аргументов *A* и *B* и для аргументов *A* и *C*, то оно истинно для аргументов *A* и *C*.

Примером отношения частичного порядка является отношение *"быть делителем"*.

**Что такое отношение happens-before?**

Отношение **happens-before**(происходит-до) является отношением частичного порядка между двумя операциями. Если одна операция **происходит до** другой, то ее результат видим и упорядочен для другой.

**Почему happens-before — это частичный порядок?**

Потому что оно удовлетворяет определению частичного порядка.

* Рефлексивность. Результат самой операции, очевидно, видим и упорядочен для нее самой.
* Антисимметричность. Если результат операции *A* видим и упорядочен для операции *B*, то результат операции *B* бы не видим и не упорядочен для *A*.

Если бы *B* была бы видна для *A*, это значит, что для ее (*A*) локального времени *B* произошла до *A*. Так как результат *A* виден для *B*, то это значит, что для ее (*B*) локального времени *A* произошла до *B*. Так как отношение "произошел до/был раньше" транзитивно, из этого следует что *B* произшла до *B*. Получаем противоречие. Следовательно, *B* не может быть видна для *A*.

* Транзитивность. Если результат операции *A* видим и упорядочен для *B* и результат *B* видим и упорядочен для *C*, то результат *A* видим и упорядочен для *С*.

Так как *A* видна для *B*, то она произошла до *B* в ее локальном времени. Так как *B* видима для *C*, то она произошла до *C* в ее локальном времени. Так как отношение "произошел до/был раньше" транзитивно, то и операция *A* видима для *C*.

**Что такое data-race?**

Data-race — это ситуация, когда несколько потоков одновременно/конкуретно работают с одной ячейкой памяти, и хотя бы один из них в нее пишет. В этом случае происходит "гонка". Результат чтения недетерминирован, так как зависит от того, какой из потоков выиграет "гонку" на физическом уровне.

**Какие гарантии на работу с volatile?**

Чтение из volatile поля *happens-before* записи в volatile поле. 

Соответственно, поток читающий из volatile поля гарантировано увидит корректное значение записи в это поля (а также всех предыдущих записей в потоке, изменившем это поле). Компилятору также запрещену переупорядочивать инструкции, которые работают с volatile полями с другими инструкциями. 

То есть JMM **гарантирует**, что записи в потоке A, которые произошли до записи в volatile-поле, произошли и видимы для потока B, который читает это поле. 

**Какие гарантии на работу с final?**

Запись в *final* поле (примитив, массив или иммутабельный объект) в конструкторе объекта сразу видима после завершение конструктора. Это позволяет быть уверенным, что иммутабельный объект корректно публикуется между потоками, даже в присутствии гонки, без дополнительной синхронизации.

**Вычисление hashCode у java.lang.String имеет data race — Вас это не пугает?**

Нет, потому что:

* поле *hash* в классе *String* имеет тип *int*. JMM гарантирует, что запись в 32-битную переменную всегда будет атомарна. Поэтому мы  можем в нем увидеть либо 0, либо корректно вычисленное значение хэш-кода.
* у нас нет необходимости сразу увидеть вычисленный хэш-код. Это поле является всего лишь оптимизацией кэширования вычисления. Если поток увидет в нем 0, то просто еще раз сделает вычисление.

Это решение видится вполне логичным инженерным компромиссом.

**Объясните смысл выражения «data race free программа является sequential consistent».**

Программа является *sequential consistent*, если ее действия на всех процессорах выполняются в каком-то последовательном порядке, и операции на каждом процессоре выполняются в программном порядке. 

*Data race free* означает, что в программе нет одновременных чтений-записей в одну ячейку памяти. Так как у нас нет конфликтов между чтениями и записями, то мы можем построить последовательный порядок, в котором выполняются операции. Внутри же процесса операции могут быть перестановлены как угодно. При этом программный порядок не нарушится, а перестановки не будут иметь эффект на другие процессоры.

**Что такое ordering, visibility, atomicity, happend-before, mutual exclusion на примере volatile**

*Ordering* - компилятору запрещено переставлять инструкции, которые работают с volatile переменными. То есть мы можем быть уверенными, что инструкции до volatile выполнились, а после него еще не начались. 

*Visibility* - записи в volatile переменные видимы для всех последующих чтений.

*Atomicity* - записи в volatile переменные атомарны в независимости от разрядности. Т.е записи volatile long и double поля всегда будут атомарны, в отличие от "голых" long и double.

*Happens-before* - с практической точни зрения это комбинация видимости и упорядочивания. Все записи в volatile поля имеют отношение 
*happens-before* по отношению к чтениям.

*Mutual exclusion* - volatile не гарантирует взаимного исключения. То есть операции с volatile полями не блокируют друг друга. volatile гарантирует, что чтение переменной увидит последнюю запись в нее, но не засчет взаимного исключения. 

**Что такое ordering, visibility, atomicity, happend-before, mutual exclusion на примере synchronize**

*Ordering* - компилятору запрещено переставлять инструкции в критической секции, выделенной с помошью *synchronized* с остальными инструкциями. То есть все операции выполняются до входа в критическую секцию и ни одна не начинается после ее окончания.

*Visibility* - записи в переменные внутри критической секции видимы для последующих чтений внутри этой же критической секции.

*Atomicity* - *synchronized* организуюет критической секцию. Все действия в критической секции выполняются только одним процессом, поэтому они являются атомарными. Конечно, при условии, что доступ к данным осуществляется только в критической секции.

*Happens-before* - вход в *synchronized* блок создает отношение *happens-before* выходу из него.

*Mutual exclusion* - *synchronized* создает критическую секцию и гарантирует взаимное исключение.

**Что такое ordering, visibility, atomicity, happend-before, mutual exclusion на примере AtomicInteger**

У *AtomicInteger* такие же гарантии на *ordering*, *visibility* и *happens-before*, как и у *volatile*, потому что поле *value* внутри него объявлено как *volatile*.

*Atomicity* - чтение и запись в *AtomicInteger* атомарны. Атомарность достигается засчет механизма CAS. CAS-инструкции позволяют атомарно обновить ячейку памяти или получить ошибку в случае, если состояние ячейки изменилось во время операции. В случае конфликта выполняется повтор операции до ее успешного выполнения.

*Mutual exclusion* - *AtomicInteger* не гарантирует взаимного исключения. Несколько процессов могут одновременно читать его значение, но писать может только один.

**Атомарно ли ++ для volatile переменной?**

Нет, так как в реальности ++ представляет из себя 3 инструкции: чтение, инкремент и запись. *volatile* гарантирует только атомарность одиночной записи (мы не увидим шум внутри поля), но не атомарность набора операций. То есть никто не запрещает 2 процессам прочитать одинаковое значение переменной, сделать инкремент и записать его в память. В этом случае мы получим "потерянную запись". 

# Базовое API, примитивы/конструкции

**Thread.getState() возвращает экземпляр Thread.State. Какие возможны значения?**

* NEW - поток был создан, но еще не стартовал; 
* RUNNABLE - поток выполняется. Этот статус не обязательно значит, что поток выполняет код. Это всего лишь значит, что поток доступен планировщику потоков для выбора потока на исполнение; 
* WAITING - поток ждет на мониторе; Поток переходит в него вызовами методов `wait()`, `join()` либо парковкой через `LockSupport.park()`
* TIME_WAITING - поток ждет на мониторе с таймаутом; Те же самые методы, только с таймаутом + `sleep`.
* BLOCKED - поток заблокирован на мониторе; Поток переходит в него когда пытается войти в критическую секцию, но она уже занята другим потокм. 
* TERMINATED - поток завершил выполнение. 

**Отличие Thread.start() и Thread.run()?**

`Thread.start()` в отличие от `Thread.run()` переводит поток в состояние RUNNABLE и запускает код независимо от текущего потока.
В случае же вызова `Thread.run()` код выполнит тот же поток, который вызвал этот метод.

Семантически эти 2 метода совершенно различны: *run* отвечает за задачу, которую нужно выполнить независимо, а *start* за поднятие инфрастуктуры по выполнению этой задачи.

**Что происходит при вызове Thread.interrupt()?**

Потоку, для которого вызвали этот метод, выставляется флаг того, что он прерван. Код, исполняющийся в этом потоке, может периодически проверять этот флаг на предмет того, что ему нужно завершить выполнение.
Если поток в момент вызова `interrupt` спал, ждал, был заблокирован на мониторе или на I/O(InterruptibleChannel), то выбросится *InterruptedException* и флаг прерывания сбросится. Программист имеет возможность обработать *InterruptedException*, выполнить действия по безопасному завершению и решить, что делать дальше (пробросить исключение, восстановить статус прерывания или завершить работу).

**Что происходит при вызове Thread.stop()?**

При вызове `Thread.stop` поток отпускает все мониторы, которые он держал, выкидавает ошибку `ThreadDeatch` и завершается принудительно.

**Почему Thread.stop()/destroy()/suspend()/resume() — deprecated?**

Потому что они небезопасны. Поток может быть прерван в любой момент и обязан отпустить все блокировки. 
То есть например во время атомарного обновления структуры данных, поток может прерваться и структура окажется в поврежденном состоянии.
Это неприемлимо. Для того, чтобы защититься в коде нужно ловить `ThreadDeath`, обрабатывать его и пробрасывать дальше. 
Но такой подход не практичен, сильно усложняет код и поддерживает плохую практику убивания ресурсов без их очистки.

**Перечислите все причины по которым может выскочить InterruptedException.**

* Поток прерван во время ожидания на мониторе
* Поток прерван во время засыпания
* Поток прерван во время захвата *ReentrantLock* через *lockInterruptibly*
* Поток прерван во время ожидания в *CountDownLatch* через *await*
* Поток прерван во время ожидания в *CyclicBarrier* через *await*
* Поток прерван во время ожидания в *Condition* через *await*
* Поток прерван во время захвата попытки в *Semaphore* через *acquireUninterruptibly*
* Поток прерван во время получения значения в *Future* через *get*
* Поток прерван во время обмена значенимя в *Exchanger* через *exchange*
* Поток прерван во время блокирующих операций с *BlockingQueue*
* Поток прерван во время работы с I/O через *InterruptableChannel*

В общем, почти любой блокирующий метод выбрасывает это исключение.

**Что из данных вызовов создает happend-before: Thread.sleep(), Thread.join(), Thread.yield(), Thread.start(), Thread.run(), Thread.isAlive(), Thread.getState()?**

Happens-before создают `Thread.start()`, `Thread.join()` и `Thread.isAlive()`. `Thread.start()` создает hb между вызовом метода и первым действием в потоке, а `Thread.join()` и `Thread.alive()` между последним действием в потоке и вызовом метода.

**В чем разница между Thread.sleep() и Thread.yeild()?**

Это совершенно разные действия. 

* `Thread.sleep` переводит поток в состояние `TIMED_WAITING` и блокирует его до окончания времени сна.
* `Thread.yeild` всего лишь является подсказкой планировщику о том, что у потока можно забрать квант времени. Поток при этом остается в состоянии `RUNNABLE`. Реализации JVM вольны игнорировать вызовы `Thread.yeild()` и практическая ценность этого метода довольно сомнительна.

**В чем проблема с Double Checked Locking? Как все-таки сделать потокозащищенный ленивый синглетон с дешевым доступом?**

Проблема в DCL в том, что это небезопасная идиома. Изначально придуманная как оптимизация производительности она ведет к тому, что синглтон в реальности не будет синглтоном.

Например, простой синглтон:
````java
public class Singleton {

    private static Singleton instance;

    private String state;

    private Singleton(String state) {
        this.state = state;
    }

    public String getState(){
        return state;
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (this) {
                if (instance == null) {
                    instance = new Singleton("I am the single one!");
                }
            }
        }
        return instance;
    }
}
````

В этом коде есть несколько проблем:
 
* Так как доступ к *instance* не синхронизирован, то никто не гарантирует безопасную публикацию этого поля. Оно публикуется через гонку. Поток, который увидит, что `instance!=null` может увидеть это поле не полностью сконструированным (например, *state* может быть *null*).
* Чтения *instance* происходят через гонку. Тот факт, что условие `instance==null` сработало не значит, чтение переменной в блоке return вернет это же значение. Там вполне может быть *null*.

Можно воспользоваться holder-идиомой. Она работает за счет того, что класс *Holder* лениво грузится и инициализуруется загрузчиком классов.
Дальше доступ идет через *synchronized*, но так критическая секция очень короткая, JVM довольно хорошо оптимизирует такие операции.

````java
public class Singleton {

    private static class Holder {
        private static final Singleton INSTANCE = new Singleton("I am the single one!");
    }

    private String state;

    private Singleton(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
````

**Что такое Safe Publishing?**

Safe Publishing - безопасная публикация. Это значит, что изменение состояние объекта будет видно тому, кто в этом заинтересован.

**Как сделать Safe Publishing используя volatile?**

Для корректной публикации достаточно объявить ссылку на объект как volatile. В этом случае все чтения увидят полностью сконструированный последний записанный объект.

**Как сделать Safe Publishing используя synchronized**

Для того, чтобы безопасно опубликовать объект с помощью synchronized, нужно изолировать все действия с ним в synchronized блоке.
Поток, входящий в критическую секцию, всегда увидит последнее корректное состояние объекта.

**Как сделать Safe Publishing используя AtomicBoolean**

У *AtomicBoolean* точно же такая семантика как и у volatile. Для того, чтобы безопасно опубликовать объект через *AtomicBoolean*, достаточно перед чтением объекта сначала прочитать *AtomicBoolean* переменную, а после изменения записать ее.
