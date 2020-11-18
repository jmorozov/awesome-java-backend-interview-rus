[Оглавление](../README.md)

# Java Core

- [Какие бывают операции в стримах? Напишите стрим?](#какие-бывают-операции-в-стримах-напишите-стрим)
- [Что поменялось с Java 8 по Java <CURRENT_VERSION>?](#что-поменялось-с-java-8-по-java-current_version)
- [В какой кодировке строки в Java? Как хранятся строки внутри класса String? Как устроен String?](#в-какой-кодировке-строки-в-java-как-хранятся-строки-внутри-класса-string-как-устроен-string)
- [Сколько в байт занимает каждый из примитивных типов в памяти? А объект?](#сколько-в-байт-занимает-каждый-из-примитивных-типов-в-памяти-а-объект)
- [Какие ссылки бывают в Java?](#какие-ссылки-бывают-в-java)

## Какие бывают операции в стримах? Напишите стрим?

Есть 2 вида операций в Java [`Stream`](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/Stream.html):
- Промежуточные (Intermediate) - `filter`, `map`, `sorted`, `peek` и т.д. Возвращают `Stream`.
- Терминальные (Terminal) - `collect`, `forEach`, `count`, `reduce`, `findFirst`, `anyMatch` и т.д. Возвращают результат стрима и запускают его выполнение.

Кроме того, будет полезно ознакомиться с содержимым пакета [java.util.stream](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/package-summary.html) и доступными коллекторами из [Collectors](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/stream/Collectors.html).

Периодически просят написать какой-нибудь стрим, поэтому хорошо бы попрактиковаться. Можно на работе наесться, можно придумать задачи самому себе, можно поискать что-нибудь готовое:
- [Java8 Code Kata](https://github.com/konohiroaki/java8-code-kata/blob/master/readme.md)
- [Experience-Java-8](https://github.com/Elqoo/Experience-Java-8/blob/master/src/main/java/academy/elqoo/java8/stream/Stream8.java)
- Может быть даже курс - [Java. Functional programming](https://stepik.org/course/1595/promo)

Почитать подробнее про стримы лучше в Java Doc, но можно и в статьях:
- [Java 8 Stream API](https://howtodoinjava.com/java8/java-streams-by-examples/)
- [The Java 8 Stream API Tutorial](https://www.baeldung.com/java-8-streams)
- [Полное руководство по Java 8 Stream API в картинках и примерах](https://annimon.com/article/2778). Тут не просто в картинках, а в анимациях!
- [Шпаргалка Java программиста 4. Java Stream API](https://habr.com/ru/company/luxoft/blog/270383/)
- [Java Stream API: что делает хорошо, а что не очень](https://habr.com/ru/company/jugru/blog/307938/)
- [Пишем свой Spliterator](https://habr.com/ru/post/256905/)

Посмотреть:
- На letsCode - [Java Stream API: функционально, модно, молодёжно!](https://www.youtube.com/watch?v=RzEiCguFZiY)
- Лекция в CSCenter от Тагира Валеева - [Лекция 8. Stream API](https://www.youtube.com/watch?v=Pk7atYm8bX0)
- Доклад Тагира Валеева на Joker 2016 - [Причуды Stream API](https://www.youtube.com/watch?v=1_Zj3gS_a3E)

[к содержанию](#java-core)

## Что поменялось с Java 8 по Java <CURRENT_VERSION>?

Java имеет [богатую историю](https://en.wikipedia.org/wiki/Java_version_history). На данный момент проекты чаще всего разделяются на:
- legacy-проекты с версией Java меньше 8
- проекты на Java 8, самая распрастранённая и популярная
- проекты на Java 9+ (точнее либо 11 LTS, либо последние полугодовые релизы)

Между 8 и 9 версиями случился небольшой разлом с частичной потерей обратной совместимости, а потом [приколы лицензирования](https://www.oracle.com/technetwork/java/javase/overview/oracle-jdk-faqs.html) подъехали, поэтому миграция и в без того консервативном мире Java-приложений идёт медленно. Однако идёт, и если вы собеседуетесь в компанию, где этот переход уже осуществили, то, вероятно, у вас поинтересуются, что же там с Java 8 поменялось, чем живёт и дышит современная Java.

На момент выхода статьи, имеем:
- [9](https://www.oracle.com/java/technologies/javase/9all-relnotes.html): [Project Jigsaw aka Модули](http://openjdk.java.net/projects/jigsaw/), [HTTP/2 Client (Incubator)](http://openjdk.java.net/jeps/110), [jshell](http://openjdk.java.net/jeps/222), [G1 GC по умолчанию](http://openjdk.java.net/jeps/248), [Compact Strings](https://openjdk.java.net/jeps/254) и [другие](https://openjdk.java.net/projects/jdk9/).
- [10](https://www.oracle.com/java/technologies/javase/10-relnote-issues.html): [Local-Variable Type Inference (var)](http://openjdk.java.net/jeps/286), [Parallel Full GC для G1](http://openjdk.java.net/jeps/307), [Graal можно использовать как основной JIT-компилятор](http://openjdk.java.net/jeps/317) и [другие](https://openjdk.java.net/projects/jdk/10/).
- [11 LTS](https://www.oracle.com/java/technologies/javase/jdk-11-relnote.html): [var в лямбдах](http://openjdk.java.net/jeps/323), [компиляция и запуск single-file программ через java](https://habr.com/ru/post/424683/), [новые методы для String](https://habr.com/ru/post/424539/), [Epsilon GC (Experimental)](http://openjdk.java.net/jeps/318), [ZGC (Experimental)](http://openjdk.java.net/jeps/333) и [другие](https://openjdk.java.net/projects/jdk/11/).
- [12](https://www.oracle.com/technetwork/java/javase/12-relnote-issues-5211422.html): [Switch Expressions (Preview)](https://openjdk.java.net/jeps/325), [Shenandoah (Experimental)](https://openjdk.java.net/jeps/189), улучшения в G1, [JMH](https://openjdk.java.net/jeps/230) и [другие](https://openjdk.java.net/projects/jdk/12/)
- [13](https://www.oracle.com/java/technologies/javase/13-relnote-issues.html): [Text Blocks (Preview)](https://openjdk.java.net/jeps/355) и [другое](https://openjdk.java.net/projects/jdk/13/)
- [14](https://www.oracle.com/technetwork/java/javase/14-relnote-issues-5809570.html): [Pattern Matching для instanceof (Preview)](https://openjdk.java.net/jeps/305), [Packaging Tool (Incubator)](https://openjdk.java.net/jeps/343), [улучшили сообщение для NullPointerExceptions](https://openjdk.java.net/jeps/358), [Records (Preview)](https://openjdk.java.net/jeps/359) и [другие](https://openjdk.java.net/projects/jdk/14/).
- [15](http://jdk.java.net/15/release-notes): [Sealed Classes (Preview)](https://openjdk.java.net/jeps/360), [Hidden Classes](https://openjdk.java.net/jeps/371), [удаление Nashorn JavaScript Engine из JDK](https://openjdk.java.net/jeps/372) и [другие](https://openjdk.java.net/projects/jdk/15/).

Найти ссылки на документацию к API, языку и виртуальной машине, release notes и сравнить API между версиями можно в [Java-альманахе](https://javaalmanac.io/).

Кроме всего прочего, есть ряд проектов, в рамках которых развиваются большие и ожидаемые сообществом изменения Java:
- [Amber](https://openjdk.java.net/projects/amber/) - проект по реализации маленьких, но продуктивных улучшений языка Java. В рамках данного проекта постепенно реализуется и независимо выходит целый набор JEP: [var](https://openjdk.java.net/jeps/286) (JDK 10), [Switch Expressions](https://openjdk.java.net/jeps/361), [Sealed Types](https://openjdk.java.net/jeps/360), [Records](https://openjdk.java.net/jeps/359), [Text Blocks](https://openjdk.java.net/jeps/368), [Pattern Matching для instanceof](https://openjdk.java.net/jeps/375) и другие.
- [Panama](https://openjdk.java.net/projects/panama/) - проект по улучшению взаимодействия между JVM и нативным кодом. На Хабре есть [статья](https://habr.com/ru/company/leader-id/blog/505072/) с разъяснениями и [интервью](https://habr.com/ru/company/jugru/blog/310014/) с Владимиром Ивановым на эту тему.
- [Loom](https://wiki.openjdk.java.net/display/loom/Main) - проект по внедрению в Java легковесных потоков. На Хабре есть две прекрасные статьи с разъяснениями: [раз](https://habr.com/ru/company/jugru/blog/422519/) и [два](https://habr.com/ru/post/503412/).
- [Valhalla](https://wiki.openjdk.java.net/display/valhalla/Main) - это проект по созданию нескольких больших и сложных улучшений языка и VM. В него входят: [Inline types](http://openjdk.java.net/jeps/169), [Generics over Primitive Types](http://openjdk.java.net/jeps/218), [Enhanced volatiles](http://openjdk.java.net/jeps/193) и другие возможные или необходимые в рамках проекта улучшения.
- [Lanai](https://wiki.openjdk.java.net/display/lanai/Main) - проект по улучшению рендеринга настольных Java-приложений на MacOS путём использования [Metal](https://developer.apple.com/metal/) Apple platform API. C 14 мая 2020 появились [Early-Access сборки](https://jdk.java.net/lanai/).
- и другие

Отдельно нужно упомянуть [GraalVM](https://www.graalvm.org/) - это JDK и виртуальная машина Java, которая создана, чтобы [объединить необъединяемое](https://habr.com/ru/company/haulmont/blog/433432/):
- быстрое выполнение Java
- уменьшение времени старта и потребления памяти для Java
- комбинирование и исполнение программ, написанных на различных ЯП, в том числе на платформо-зависимых
- общие инструменты для всех ЯП
- поддержка JIT и AOT-компиляции
- и т.п.

Послушать на тему:
- Два выпуска подкаста Javaswag: [раз](https://soundcloud.com/javaswag/8-evgeniy-kozlov-pochemu-ne-c-var-zlo-ili-net-novye-fichi-java-9-12) и [два](https://soundcloud.com/javaswag/9-evgeniy-kozlov-den-rozhdeniya-java-i-novye-fichi-java-12-14)
- [Выпуск 172 Java](https://podlodka.io/172) подкаста Подлодка, в гости к которому пришёл Тагир Валеев

Почитать на Хабре:
- [Руководство по возможностям Java версий 8-14](https://habr.com/ru/post/500468/)
- [API, ради которых наконец-то стоит обновиться с Java 8. Часть 1](https://habr.com/ru/post/485750/)
- [JAVA 9. Что нового?](https://habr.com/ru/company/otus/blog/341058/)
- [Обзор Java 9](https://habr.com/ru/post/342170/)
- [Модульность в Java 9](https://habr.com/ru/post/499872/)
- [Компактные строки в Java 9](https://habr.com/ru/company/otus/blog/426621/)
- [Java 10 General Availability](https://habr.com/ru/company/jugru/blog/351694/)
- [Изменения в стандартной библиотеке Java 10](https://habr.com/ru/post/349868/)
- [Записки о миграции на Java 10](https://habr.com/ru/post/354114/)
- [Как Java 10 изменяет способ использования анонимных внутренних классов](https://habr.com/ru/company/otus/blog/456010/)
- ["Жизнь после Java 10": какие изменения принесет Java 11](https://habr.com/ru/company/it-grad/blog/358810/)
- [90 новых фич (и API) в JDK 11](https://habr.com/ru/post/424683/)
- [Java 11: новое в String](https://habr.com/ru/post/424539/)
- [Java 11 / JDK 11: General Availability](https://habr.com/ru/company/jugru/blog/424543/)
- [39 новых фич, которые будут доступны в Java 12](https://habr.com/ru/post/446590/)
- [Пришло время Java 12! Обзор горячих JEP-ов](https://habr.com/ru/company/jugru/blog/444434/)
- [Новое в Java 12: The Teeing Collector](https://habr.com/ru/post/445622/)
- [Только что вышла Java 13](https://habr.com/ru/company/jugru/blog/467789/)
- [В Java 13 хотят добавить "текстовые блоки"](https://habr.com/ru/post/454144/)
- [Introducing Java 13: Let's dive Into JDK's New Features](https://habr.com/ru/post/483186/)
- [Что нового будет в Java 14](https://habr.com/ru/company/alconost/news/t/491378/)
- [Java 14 is coming](https://habr.com/ru/post/491546/)
- [Java 14: Record, более лаконичный instanceof, упаковщик jpackage, switch-лямбды и текстовые блоки](https://habr.com/ru/post/491564/)
- [Исследуем записи в Java 14](https://habr.com/ru/post/482300/)
- [Пробуем улучшенный оператор instanceof в Java 14](https://habr.com/ru/post/477654/)
- [Исследуем sealed классы в Java 15](https://habr.com/ru/post/505696/)
- [Sealed classes. Semantics vs performance](https://habr.com/ru/post/430014/)
- [Sealed типы в Java](https://habr.com/ru/post/490298/)
- [Что нового в Java 15?](https://habr.com/ru/post/518794/)
- [Вышла Java 15](https://habr.com/ru/post/519270/)
- [Project Panama: как сделать Java "ближе к железу"?](https://habr.com/ru/company/leader-id/blog/505072/)
- [Раздача халявы: нетормозящие треды в Java. Project Loom](https://habr.com/ru/company/jugru/blog/422519/)
- [Project Loom: виртуальные потоки в Java уже близко](https://habr.com/ru/post/503412/)
- [Десять вещей, которые можно делать с GraalVM](https://habr.com/ru/company/haulmont/blog/433432/)
- [Как работает Graal — JIT-компилятор JVM на Java](https://habr.com/ru/post/419637/)
- [Graal: как использовать новый JIT-компилятор JVM в реальной жизни](https://habr.com/ru/company/billing/blog/351682/)
- [Разрабатываем утилиту на GraalVM](https://habr.com/ru/post/451574/)
- [Скрещиваем ужа с ежом: OpenJDK-11 + GraalVM](https://habr.com/ru/post/445978/)
- [JavaScript, Java, какая теперь разница?](https://habr.com/ru/company/jugru/blog/353624/)
- [Что под капотом компиляторных оптимизаций GraalVM?](https://habr.com/ru/company/jugru/blog/485024/)

И не только:
- [Java-альманах](https://javaalmanac.io/)
- State of Loom: [часть 1](http://cr.openjdk.java.net/~rpressler/loom/loom/sol1_part1.html) и [часть 2](http://cr.openjdk.java.net/~rpressler/loom/loom/sol1_part2.html)
- [GraalVM](https://www.graalvm.org/)

Посмотреть:
- [Cay Horstmann — Feature evolution in Java 13 and beyond](https://www.youtube.com/watch?v=Gvs-nGUiqFA)
- [Тагир Валеев — Java 9-14: Маленькие оптимизации](https://www.youtube.com/watch?v=5Y0Alqb9H_I)
- [Никита Липский — Java 9 Модули. Почему не OSGi?](https://www.youtube.com/watch?v=hEnvRXNyDgI)
- [Cay Horstmann — Java 9: the good parts (not modules)](https://www.youtube.com/watch?v=_cvgg9szPH8)
- [Владимир Иванов - Project Panama: как сделать Java “ближе к железу”?](https://www.youtube.com/watch?v=4vHMmLqF09Y)
- [Олег Чирухин — GraalVM Всемогущий](https://www.youtube.com/watch?v=lH4H0LEAo9g)
- [Олег Чирухин — Graal, Value Types, Loom и прочие ништяки](https://www.youtube.com/watch?v=hnadkXBIC1k)
- [Олег Шелаев — Компилируем Java ahead of time с GraalVM](https://www.youtube.com/watch?v=tPezgDSD1Bk)
- [Олег Шелаев — Суперкомпиляция, partial evaluation, проекции Футамуры и как GraalVM спасет мир](https://www.youtube.com/watch?v=C5fATKdVxZ0)
- [Project Loom](https://www.youtube.com/watch?v=wWAxD7STTFQ) и [Новое в JDK 14](https://www.youtube.com/watch?v=qN8x-aIrhxk) на letsCode
- [GOTO 2019 • Life After Java 8 • Trisha Gee](https://www.youtube.com/watch?v=eBuFzQeiGe0)
- [Dalia Abo Sheasha — Migrating beyond Java 8](https://www.youtube.com/watch?v=NeYYmIup5nY)
- [Project Loom: Helping Write Concurrent Applications on the Java Platform by Ron Pressler](https://www.youtube.com/watch?v=lIq-x_iI-kc)

[к содержанию](#java-core)

## В какой кодировке строки в Java? Как хранятся строки внутри класса String? Как устроен String?

До Java 9 все строки имели кодировку `UTF-16` (2 байта на символ) и хранились в массиве `char`.

С Java 9 пришло такое изменение как [Compact String](https://openjdk.java.net/jeps/254). Если все символы строки входят в множество символов `Latin-1` (а это подавляющее большинство строк), то каждый из них может поместиться в 1 байт, поэтому в этом случае массив `char` избыточен. В результате было принято решение заменить массив `char` на массив `byte`, что позволяет строкам `Latin-1` расходовать меньше памяти. Кодировка строки хранится в отдельном поле `byte coder`, значение которого представляет `Latin-1` или `UTF-16`.

Также интересной особенностью является кеширование классом `String` своего `hashcode`.

Строки являются неизменяемыми, наследоваться от строк запрещено (`final class`). Все операции по изменении строки возвращают её новый экземпляр, в том числе и конкатенация строк. Компилятор [умеет оптимизировать конкатенацию](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.18.1) и превращать её в объект [StringBuilder](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/StringBuilder.html) и совокупность вызовов методов `append`. ОДНАКО! В Java 9 вошёл [JEP 280: Indify String Concatenation](https://openjdk.java.net/jeps/280), который изменил эту оптимизацию и пошёл ещё дальше. Теперь вместо `StringBuilder` генерируется bytecode для вызова [StringConcatFactory](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/StringConcatFactory.html) через [invokedynamic](https://www.infoq.com/articles/Invokedynamic-Javas-secret-weapon/), поэтому стоит расслабиться и чаще выбирать `+`.

Ещё можно упомянуть про `String pool` - это выделяемое в `heap` пространство, которое используется для оптимизации потребления памяти при хранении строк. Благодаря ему одинаковые строковые литералы могут ссылаться на один и тот же объект.

Стоит помнить, что с помощью [String.intern()](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/String.html#intern()) производительности особой не добиться, а можно наоборот пустить всё по миру. Лучше напишите свою реализацию. Подоробнее читайте в статье Алексея Шипилёва - [JVM Anatomy Quark #10: String.intern()](https://shipilev.net/jvm/anatomy-quarks/10-string-intern/).

Кроме того, [equals](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/String.html#equals(java.lang.Object)) и методы поиска (например [indexOf](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/String.html#indexOf(int))) [оптимизируются JIT компилятором](https://habr.com/ru/post/352086/) на нативном уровне.

Посмотреть доклады Алексея Шипилёва на тему строк: [Катехизис java.lang.String](https://www.youtube.com/watch?v=SZFe3m1DV1A) и [The Lord of the Strings: Two Scours](https://www.youtube.com/watch?v=HWkVJkoo1_Q).

Подробнее:
- [String javadoc](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/String.html)
- [Как обойти строчку?](https://itsobes.ru/JavaSobes/kak-oboiti-strochku/)
- [Из чего состоит String?](https://itsobes.ru/JavaSobes/iz-chego-sostoit-string/)
- [JDK 9/JEP 280: конкатенация строк никогда больше не будет прежней](https://habr.com/ru/company/otus/blog/444822/) на Хабре
- [Компактные строки в Java 9](https://habr.com/ru/company/otus/blog/426621/) на Хабре
- [Guide to Java String Pool](https://www.baeldung.com/java-string-pool) на Baeldung
- [Compact Strings in Java 9](https://www.baeldung.com/java-9-compact-string)
- [Владимир Иванов — Глубокое погружение в invokedynamic](https://www.youtube.com/watch?v=DgshYDTpS9I)
- [Charles Nutter — Let's Talk About Invokedynamic](https://www.youtube.com/watch?v=0vfFHMGESVQ)
- [Что там с JEP-303 или изобретаем invokedynamic](https://habr.com/ru/post/328240/)

[к содержанию](#java-core)

## Сколько в байт занимает каждый из примитивных типов в памяти? А объект?
  
Казалось бы:
- `byte` - 1 байт
- `short` - 2 байта
- `int` - 4 байта
- `long` - 8 байт
- `char` - 2 байта
- `float` - 4 байта
- `double` - 8 байт

А размер boolean не упоминается в спецификации вовсе. Однако также спецификация не запрещает использовать для хранения примитива больше памяти - главное, чтобы размер был достаточным для всех значений. Конкретный объём таки зависит от реализации JVM. Не последнюю роль в этом играет [выравнивание данных](https://en.wikipedia.org/wiki/Data_structure_alignment) в памяти.

Похожая ситуация и со ссылочными типами - спецификация JVM не требует какой-то определённой структуры для объектов и отдаёт её на откуп реализации. Все тонкости и секреты занимаемой объектами памяти раскрывает Алексей Шипилёв в своей статье [Java Objects Inside Out](https://shipilev.net/jvm/objects-inside-out/).

Подробнее:
- [The Java Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se15/html/jvms-2.html#jvms-2.2)
- [Какие существуют примитивы?](https://itsobes.ru/JavaSobes/kakie-sushchestvuiut-primitivy/)
- [Сколько памяти занимает объект?](https://itsobes.ru/JavaSobes/skolko-pamiati-zanimaet-obekt/)
- [Какие существуют примитивы?](https://itsobes.ru/JavaSobes/kakie-sushchestvuiut-primitivy/)
- [Размер Java объектов](https://habr.com/ru/post/134102/) на Хабре
- [Java Objects Inside Out](https://shipilev.net/jvm/objects-inside-out/)
- [jol](https://github.com/openjdk/jol)
- [Как JVM аллоцирует объекты?](https://habr.com/ru/post/332708/) на Хабре
- [Сжатие указателей в Java](https://habr.com/ru/post/440166/) на Хабре
- [Measuring Object Sizes in the JVM](https://www.baeldung.com/jvm-measuring-object-sizes) на Bealdung

Если вас заинтересовало представление объектов в jvm и их реализация (и вы умеете-могёте читать C++), то можно пойти посмотреть исходники openjdk. Начать, например, отсюда:
- [instanceOop.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/oops/instanceOop.hpp)
- [klass.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/oops/klass.hpp)
- [instanceKlass.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/oops/instanceKlass.hpp)
- [objArrayKlass.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/oops/objArrayKlass.hpp)
- [objArrayOop.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/oops/objArrayOop.hpp)
- [oopFactory.hpp](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/memory/oopFactory.hpp)

[к содержанию](#java-core)

## Какие ссылки бывают в Java?

Типы ссылок в Java:
- `Strong reference` - обычная переменная ссылочного типа в Java. Объект такой ссылки очищается GC не раньше, чем станет неиспользуемым (никто нигде на него больше не ссылается).
- [Слабые ссылки](https://en.wikipedia.org/wiki/Weak_reference) - сборщик мусора тем или иным образом не учитывает связь ссылки и объекта в куче при выявлении объектов, подлежащих удалению. Объект будет удалён даже при наличии слабой ссылки на него:
- `Soft reference` - мягкая ссылка, экземпляр класса [SoftReference](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/ref/SoftReference.html). Объект гарантированно будет собран GC до возникновения [OutOfMemoryError](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/OutOfMemoryError.html). Может использоваться для реализации кэшей, увеличивающихся без риска `OutOfMemoryError` для приложения.
- `Weak reference` - слабая ссылка, экземпляр класса [WeakReference](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/ref/WeakReference.html). Не препятствует утилизации объекта и игнорируется GC при сборке мусора. Может использоваться для хранения некоторой связанной с объектом информации до момента его смерти. Также стоит обратить внимание на [WeakHashMap](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/util/WeakHashMap.html).
- `Phantom reference` - фантомная ссылка, экземпляр класса [PhantomReference](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/ref/PhantomReference.html). Не препятствует утилизации объекта и игнорируется GC при сборке мусора и имеет ряд особенностей, описанных ниже. Может быть применена для получения уведомления, что объект стал неиспользуемым и можно освободить связанные с ним ресурсы (как более надёжный вариант, чем `finalize()`, вызов которого не гарантируется, может проводить сеансы воскрешения и вообще deprecated).

Чтобы достать объект из слабых ссылок, необходимо вызывать метод `get()`. Если объект недостижим, то метод вернёт `null`. Для фантомных ссылок всегда возвращается `null`.

При создании слабой ссылки в конструктор можно, а для `PhantomReference` необходимо, передать экземпляр [ReferenceQueue](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/ref/ReferenceQueue.html) - в очереди будет сообщение, когда ссылка протухнет. Для `SoftReference` и `WeakReference` это будет ДО финализации объекта, а для `PhantomReference` ПОСЛЕ. Однако фактическое удаление объекта фантомной ссылки из памяти не производится до момента её очистки.

Подробнее:
- [Мягкие ссылки на страже доступной памяти или как экономить память правильно](https://habr.com/ru/post/169883/) на Хабре
- [Использование PhantomReferences в Java](http://samolisov.blogspot.com/2011/09/phantomreferences-java.html)

[к содержанию](#java-core)