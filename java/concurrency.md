[Оглавление](../README.md)

# Java Concurrency

- [Расскажите про методы `wait`, `notify`, `notifyAll` и ключевое слово `synchronized`](#расскажите-про-методы-wait-notify-notifyall-и-ключевое-слово-synchronized)
- [JMM. Зачем нужно volatile. Популярный вопрос](#jmm-зачем-нужно-volatile-популярный-вопрос)
- [Что такое Executor и ExecutorService, Thread pool и зачем нужны?](#что-такое-executor-и-executorservice-thread-pool-и-зачем-нужны)
- [Что внутри параллельных стримов? На каком пуле работают параллельные стримы и в чем его особенность?](#что-внутри-параллельных-стримов-на-каком-пуле-работают-параллельные-стримы-и-в-чем-его-особенность)
- [Как работает ConcurrentHashMap?](#как-работает-concurrenthashmap)
- [Как работают Атомики?](#как-работают-атомики)
- [Что такое ThreadLocal переменные?](#что-такое-threadlocal-переменные)
- [Чем поток отличается от процесса?](#чем-поток-отличается-от-процесса)

## Расскажите про методы `wait`, `notify`, `notifyAll` и ключевое слово `synchronized`

В принципе, статьи на [Baeldung](https://www.baeldung.com/java-wait-notify) должно хватить. Лучше, конечно, пописать код с использованием `wait`, `notify`, `notifyAll` и `synchronized` руками. Также можно почитать [официальный туториал](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html) от Oracle по Concurrency в Java.

Но если хотите пойти глубже, то хаброписатели опять спешат на помощь - [тут](https://habr.com/ru/post/143237/). А также Java Language Specification, [раздел 17.1 и 17.2](https://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html).

[к содержанию](#java-concurrency)

## JMM. Зачем нужно volatile. Популярный вопрос

Не знаю как у вас, но у меня при упоминании JMM молниеносно всплывает в голове Алексей Шипилёв и его доклады - [раз](https://www.youtube.com/watch?v=noDnSV7NCtw), [два](https://www.youtube.com/watch?v=Ky1_5mabd18), [три](https://www.youtube.com/watch?v=C6b_dFtujKo). Если вы больше чтец, чем смотрец, то Алексея можно и почитать - [ать](https://shipilev.net/blog/2014/jmm-pragmatics/), [два](https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/).

Кроме того, абсолютно не будет лишним посмотреть [доклад](https://www.youtube.com/watch?v=hxIRyqHRnjE&list=PLU17dD8nR0m7GusjRbl42KE94a3dlpx_a&index=3&t=0s) Романа Елизарова по теоретическому минимуму JMM.

Если совсем нет времени, то можно пробежаться по небольшой [статейке](http://www.javaspecialist.ru/2011/06/java-memory-model.html) по JMM. Если есть время и интерес, тогда углубляемся в тему через [статью](https://habr.com/ru/post/440590/) на Хабре. А ещё на Хабре есть неплохой перевод статьи "Многопоточность. Java-модель памяти": [часть 1](https://habr.com/ru/post/510454/) и [часть 2](https://habr.com/ru/post/510618/).

Несомненным источником истины является Java Language Specification, [раздел 17.4](https://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html).

Также ответ на этот вопрос можно [прочитать](https://itsobes.ru/JavaSobes/chto-delaet-volatile/) на itsobes.ru.
А ещё можно ознакомиться с вопросом на JVM-уровне в статье [How ‘volatile’ works on JVM level?](https://medium.com/@dredwardhyde/how-volatile-works-on-jvm-level-7a250d38435d) на Medium.

[к содержанию](#java-concurrency)

## Что такое Executor и ExecutorService, Thread pool и зачем нужны?

Создавать и убивать потоки - дорого. Давайте создадим N потоков (Thread pool) и будем их переиспользовать. А давайте. Вот [тут](https://www.ibm.com/developerworks/ru/library/j-jtp0730/index.html) описано развёрнуто.

[Executor](https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/concurrent/Executor.html) (`void execute​(Runnable command)` - вот и весь интерфейс) и [ExecutorService](https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/concurrent/ExecutorService.html) (уже покруче, может запускать Callable и не только) - грубо говоря, интерфейсы выполняторов параллельных задач. А реализуют их различные выполняторы на пулах потоков. Экземпляры готовых конкретных выполняторов можно получить с помощью класса [Executors](https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/concurrent/Executors.html). Если смелый-умелый и зачем-то надо, то можно и самому реализовать, конечно.

Также подробнее можно почитать:
- [Cтатью](https://habr.com/ru/post/326146/) на Хабре
- На Baeldung: [раз](https://www.baeldung.com/java-executor-service-tutorial) и [два](https://www.baeldung.com/thread-pool-java-and-guava)
- Официальный [туториал](https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html) от Oracle

[к содержанию](#java-concurrency)

## Что внутри параллельных стримов? На каком пуле работают параллельные стримы и в чем его особенность?

По умолчанию parallel stream использует [ForkJoinPool.commonPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html#commonPool--) размером `Runtime.getRuntime().availableProcessors() — 1`. Common pool создаётся статически при первом обращении к `ForkJoinPool` и живёт до `System::exit` (игнорирует `shutdown()` или `shutdownNow()`). Когда некий поток отправляет задачу в common pool, то pool может использовать его же в качестве воркера. Common pool один на всё приложение. Можно запустить stream на [отдельном](https://www.baeldung.com/java-8-parallel-streams-custom-threadpool) `ForkJoinPool` - завернуть параллельный stream в `Callable` и передать на вход методу `submit` созданного `ForkJoinPool`. Этот трюк работает благодаря методу [fork()](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinTask.html#fork--) из `ForkJoinPool` ([тут](https://blog.krecan.net/2014/03/18/how-to-specify-thread-pool-for-java-8-parallel-streams/) подробности).

Сам по себе [ForkJoinPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html) представляет реализацию [ExecutorService](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html), выполняющую [ForkJoinTask](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinTask.html) ([RecursiveAction](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/RecursiveAction.html) и [RecursiveTask](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/RecursiveTask.html)). Данный pool создан для упрощения распараллеливания рекурсивных задач и утилизации породивших подзадачу потоков. `ForkJoinPool` использует подход [work stealing](https://en.wikipedia.org/wiki/Work_stealing) - у каждого потока есть его локальная очередь задач, из хвоста которой другие потоки могут тырить себе задачи, если у них закончились свои. Украденная задача делится и заполняет очередь задач потока.

Подробнее:
- В статьях [Stream API & ForkJoinPool](https://habr.com/ru/company/otus/blog/338770/) и [Fork/Join Framework в Java 7](https://habr.com/ru/post/128985/) на Хабре
- Посмотреть доклад Алексея Шипилёва [ForkJoinPool в Java 8](https://www.youtube.com/watch?v=t0dGLFtRR9c)
- В статьях [Guide to the Fork/Join Framework in Java](https://www.baeldung.com/java-fork-join) и [Guide to Work Stealing in Java](https://www.baeldung.com/java-work-stealing) на Baeldung
- JavaDoc к [ForkJoinPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html)
- В статье [Think Twice Before Using Java 8 Parallel Streams](https://dzone.com/articles/think-twice-using-java-8) на DZone
- В статье [Java Parallel Streams Are Bad for Your Health!](https://www.jrebel.com/blog/parallel-java-streams) в блоге JRebel
- С примерами и картинками - [Java Parallel Stream](https://java2blog.com/java-8-parallel-stream/)
- С графиками в [How does the Fork/Join framework act under different configurations?](https://blog.overops.com/forkjoin-framework-vs-parallel-streams-vs-executorservice-the-ultimate-benchmark/)
- [Как работают параллельные стримы?](https://itsobes.ru/JavaSobes/kak-rabotaiut-parallelnye-strimy/)

[к содержанию](#java-concurrency)

## Как работает ConcurrentHashMap?
[ConcurrentHashMap](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html) - это потокобезопасная мапа (карта, словарь, ассоциативный массив, но тут и далее просто "мапа"), у которой отсутствуют блокировки на всю мапу целиком.

Особенности реализации:
- Поля элемента мапы (`Node<K,V>`) `val` (значение) и `next`(следующее значение по данному ключу в цепочке или дереве), а также таблица бакетов (`Node<K,V>[] table`) объявлены как `volatile`
- Для операций вставки первого элемента в бакет используется [CAS](https://en.wikipedia.org/wiki/Compare-and-swap) - алгоритм, а для других операций обновления в этой корзине (insert, delete, replace) блокировки
- Каждый бакет может блокироваться независимо путём блокировки первого элемента в корзине
- Таблице бакетов требуется volatile/atomic чтения, запись и CAS, поэтому используются intrinsics-операции (`jdk.internal.misc.Unsafe`)
- Concurrent resizing таблицы бакетов
- Ленивая инициализация таблицы бакетов
- При подсчёте количества элементов используется специальная реализация [LongAdder](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/LongAdder.html)

В результате имеем:
- Извлечение значения возвращает последний результат завершенного обновления мапы на момент начала извлечения. Или перефразируя, любой `non-null` результат, возвращаемый `get(key)` связан отношением `happens-before` со вставкой или обновлением по этому ключу
- Итераторы по `ConcurrentHashMap` возвращают элементы отображающие состояние мапы на определённый момент времени - они не бросают `ConcurrentModificationException`, но предназначены для использования одним потоком одновременно
- Нельзя полагаться на точность агрегирующих методов (`size`, `isEmpty`, `containsValue`), если мапа подвергается изменениям в разных потоках
- Не позволяет использовать `null`, который однозначно воспринимается как отсутствие значения
- Поддерживает потокобезопасные, затрагивающие все (или многие) элементы мапы, операции - `forEach`, `search`, `reduce` (bulk operations). Данные операции принимают на вход функции, которые не должны полагаться на какой-либо порядок элементов в мапе и в идеале должны быть чистыми (за исключением `forEach`). На вход данные операции также принимают `parallelismThreshold` - операции будут выполняться последовательно, если текущий размер мапы меньше `parallelismThreshold`. Значение `Long.MAX_VALUE` сделает операцию точно последовательной. Значение `1` максимизирует параллелизм и утилизацию `ForkJoinPool.commonPool()`, который будет использоваться для параллельных вычислений

На Хабре есть несколько устаревшая [статья](https://habr.com/ru/post/132884/) - будьте внимательны и осторожны с java 8 произошли изменения. Класс `Segment<K,V>` максимально урезан и сохранён только для обратной совместимости при сериализации, где и используется. `concurrencyLevel` также оставлен лишь для обратной совместимости и теперь служит в конструкторе только для увеличения `initialCapacity` до количества предполагаемых потоков-потребителей мапы:
```java
    if (initialCapacity < concurrencyLevel)   // Use at least as many bins
        initialCapacity = concurrencyLevel;   // as estimated threads
```
Есть более современная [статья](https://habr.com/ru/post/327186/) с примером реализации ConcurrentMap. Также можно почитать [гайд](https://www.baeldung.com/java-concurrent-map) по ConcurrentMap на Baeldung.

[к содержанию](#java-concurrency)

## Как работают Атомики?

Атомарная операция — это операция, которая выполняется полностью или не выполняется совсем, частичное выполнение невозможно.

Атомики - это классы, которые выполняют операции изменения своего значения атомарно, т.о. они поддерживают lock-free thread-safe использование переменных. Достигается это с помощью алгоритма [compare-and-swap (CAS)](https://en.wikipedia.org/wiki/Compare-and-swap) и работает быстрее, чем аналогичные реализации с блокировками. На уровне инструкций большинства процессоров имеется поддержка CAS.

В общем случае работу Атомиков можно описать следующим образом. Атомик хранит некоторое `volatile` значение `value`, для изменения которого используется метод `compareAndSet(current, new)`, поэтому предварительно читается текущее значение - `current`. Данный метод с помощью CAS изменяет значение `value` только в том случае, если оно равно ожидаемому значению (т.е. `current`), прочитанному перед запуском `compareAndSet(current, new)`. Если значение `value` было изменено в другом потоке, то оно не будет равно ожидаемому. Следовательно, метод `compareAndSet` вернет значение `false`. Поэтому следует повторять попытки чтения текущего значения и запуска с ним метода `compareAndSet(current, new)` пока `current` не будет равен `value`.

Условно можно разделить методы Атомиков на:
- `compare-and-set` - принимают `current` на вход и делают одну попытку записи через CAS
- `set-and-get` - самостоятельно читают `current` и пытаются изменить значение с помощью CAS в цикле, как описано выше

Непосредственно изменение значения `value` делегируется либо [VarHandle](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/invoke/VarHandle.html), либо [Unsafe](https://github.com/openjdk/jdk/blob/master/src/java.base/share/classes/jdk/internal/misc/Unsafe.java), которые в свою очередь выполняют его на нативном уровне. VarHandle - это динамически сильно типизированная ссылка на переменную или на параметрически определяемое семейство переменных, включающее статические поля, нестатические поля, элементы массива или компоненты структуры данных нестандартного типа. Доступ к таким переменным поддерживается в различных режимах, включая простой доступ на чтение/запись, `volotile` доступ на чтение/запись и доступ на `compare-and-swap`.

В [java.util.concurrent.atomic](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/package-summary.html) имеется следующий набор атомиков:
- [AtomicBoolean](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicBoolean.html), [AtomicInteger](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicInteger.html), [AtomicLong](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicLong.html), [AtomicIntegerArray](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicIntegerArray.html), [AtomicLongArray](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicLongArray.html) - представляют атомарные целочисленные, булевы примитивные типы, а также два массива атомарных целых чисел.
- [AtomicReference](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicReference.html) - класс для атомарных операций со ссылкой на объект.
- [AtomicMarkableReference](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicMarkableReference.html) - класс для атомарных операций над парой `[reference, boolean]`.
- [AtomicStampedReference](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicStampedReference.html) - класс для атомарных операций над парой `[reference, int]`.
- [AtomicReferenceArray](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicReferenceArray.html) - массив атомарных ссылок
- [AtomicIntegerFieldUpdater](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicIntegerFieldUpdater.html), [AtomicLongFieldUpdater](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicLongFieldUpdater.html), [AtomicReferenceFieldUpdater](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/AtomicReferenceFieldUpdater.html) - классы для атомарного обновления полей по их именам через reflection.
- [DoubleAccumulator](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/DoubleAccumulator.html), [LongAccumulator](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/LongAccumulator.html) - классы, представляющие атомарные аккумуляторы, которые принимают на вход чистую функцию-аккумулятор (`BinaryOperator`) и начальное значение. Сохраняет весь набор операндов, а когда необходимо получить значение, то аккумулирует их с помощью функции-аккумулятора. Порядок операндов и применения функции-аккумулятора не гарантируется. Используется, когда записей намного больше, чем чтения.
- [DoubleAdder](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/DoubleAdder.html), [LongAdder](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/concurrent/atomic/LongAdder.html) - классы, представляющие атомарные счётчики. Являются частным случаем атомарных аккумуляторов, у которых функция-аккумулятор выполняет простое суммирование, а начальным значением является 0.

С помощью атомиков можно реализовать блокировку, например так:
```java
    public class NonReentrantSpinLock {

        private AtomicReference<Thread> owner = new AtomicReference<>();

        public void lock() {
          Thread currentThread = Thread.currentThread();

          while (!owner.compareAndSet(null, currentThread)) {}
        }

        public void unlock() {
          Thread currentThread = Thread.currentThread();
          owner.compareAndSet(currentThread, null);
        }
    }
```

Подробнее:
- [Как устроены атомики?](https://itsobes.ru/JavaSobes/kak-ustroeny-atomiki/)
- [Compare and Swap](http://tutorials.jenkov.com/java-concurrency/compare-and-swap.html)
- [Обзор java.util.concurrent.*](https://habr.com/ru/company/luxoft/blog/157273/) на Хабре
- [Разбор основных концепций параллелизма](https://habr.com/ru/company/otus/blog/353414/) на Хабре
- [Книга "Java Concurrency на практике"](https://habr.com/ru/company/piter/blog/489038/) - её отрывок на Хабре
- [JDK concurrent package](https://habr.com/ru/post/187854/) на Хабре
- [Atomic operations](https://habr.com/ru/post/157163/) на Хабре
- [Concurrency: 6 способов жить с shared state](https://habr.com/ru/post/216049/) на Хабре
- [The Art of Multiprocessor Programming](https://www.amazon.com/dp/0123705916)
- [The JSR-133 Cookbook for Compiler Writers](http://gee.cs.oswego.edu/dl/jmm/cookbook.html)
- [AtomicReference: A (Sometimes Easier) Alternative to Synchronized Blocks](https://dzone.com/articles/atomicreference-a-sometimes-easier-alternative-to)
- [An Introduction to Atomic Variables in Java](https://www.baeldung.com/java-atomic-variables) на Bealdung
- [Переход к атомарности](https://www.ibm.com/developerworks/ru/library/j-jtp11234/index.html)
- [Use AtomicReference to implement Reentrant Lock](http://www.java2s.com/example/java/thread/use-atomicreference-to-implement-reentrant-lock.html)
- [A comprehensive understanding of Java atomic variable classes](https://programmer.ink/think/a-comprehensive-understanding-of-java-atomic-variable-classes.html)
- [Faster Atomic*FieldUpdaters for Everyone](https://shipilev.net/blog/2015/faster-atomic-fu/)
- [Алексей Шипилёв — Если не Unsafe, то кто: восход VarHandles](https://www.youtube.com/watch?v=ESs0bZw8hsA)
- [Introduction to nonblocking algorithms](https://www.ibm.com/developerworks/java/library/j-jtp04186/index.html)

[к содержанию](#java-concurrency)

## Что такое ThreadLocal переменные?

[ThreadLocal](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/ThreadLocal.html) - класс в виде обёртки для хранения отдельной независимой копии значения переменной для каждого использующего её потока, что позволяет сделать работу с такой переменной потокобезопасной.

Данные `ThreadLocal`-переменных хранятся не в них самих, а непосредственно в объектах `Thread`. У каждого экземпляра класса [Thread](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Thread.html) есть поле `ThreadLocal.ThreadLocalMap threadLocals`, которое инициализируется и используется `ThreadLocal`. `ThreadLocal.ThreadLocalMap` представляет собой специализированную версию `HashMap`, записи которой наследуют от `WeakReference<ThreadLocal<?>>`, используя ключ мапы как `ref field` слабой ссылки. Ключами такой мапы являются `ThreadLocal`, а значением - `Object`. Если ключ записи равен `null`, то такая запись называется просроченной (`stale`) и будет удалена из мапы.

Следует обратить внимание, что `ThreadLocal` изолирует именно ссылки на объекты, а не копии их значений. Если изолированные внутри потоков ссылки ведут на один и тот же объект, то возможны коллизии.

Когда у `ThreadLocal`-переменной запрашивается её значение (например через метод `get`), то она получает текущий поток, извлекает из него мапу `threadLocals`, и получает значение из мапы, используя себя в качестве ключа. Аналогично выполняются методы изменения значения `ThreadLocal`.

Из этого следует, что значение `ThreadLocal`-переменной должно устанавливаться в том же потоке, в котором оно будет использоваться.

Подробнее:
- [Зачем используются thread local переменные?](https://itsobes.ru/JavaSobes/zachem-ispolzuiutsia-thread-local-peremennye/)
- [О бедном ThreadLocal замолвите слово](http://samolisov.blogspot.com/2011/04/threadlocal.html)
- [Использование ThreadLocal переменных](https://articles.javatalks.ru/articles/17)
- [An Introduction to ThreadLocal in Java](https://www.baeldung.com/java-threadlocal)
- [Утечка памяти с ThreadLocal](https://habr.com/ru/company/maxifier/blog/218313/) на Хабре
- [5 вещей, которых вы не знали о многопоточности](https://habr.com/ru/post/108016/) на Хабре
- [Разбор основных концепций параллелизма](https://habr.com/ru/company/otus/blog/353414/) на Хабре

[к содержанию](#java-concurrency)

## Чем поток отличается от процесса?

Ответ на этот вопрос писали в интернете ещё тогда, когда я балду в школе пинал (нулевые), поэтому у читателя есть широкий выбор.
Мне импонирует [заметка](https://www.opennet.ru/docs/RUS/linux_parallel/node42.html) на OpenNET - кратко и чётко, суше Сахары.
Для любителей смотреть в окна есть [статейка](https://docs.microsoft.com/ru-ru/windows/win32/procthread/about-processes-and-threads?redirectedfrom=MSDN) на MSDN.
Вишенка на торте - олдовая [статья](https://habr.com/ru/post/40227/) с Хабра.

[к содержанию](#java-concurrency)