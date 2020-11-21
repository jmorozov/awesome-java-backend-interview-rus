[Оглавление](../README.md)

# JVM

- [Сборка мусора. Как работает? Какие сборщики знаете? Какие есть области памяти в JVM? Что будет с двумя или более объектами, которые ссылаются только друг на друга, но больше не на кого и никому не нужны - как с ними поступит сборщик и как именно это будет делать?](#сборка-мусора-как-работает-какие-сборщики-знаете-какие-есть-области-памяти-в-jvm-что-будет-с-двумя-или-более-объектами-которые-ссылаются-только-друг-на-друга-но-больше-не-на-кого-и-никому-не-нужны---как-с-ними-поступит-сборщик-и-как-именно-это-будет-делать)

## Сборка мусора. Как работает? Какие сборщики знаете? Какие есть области памяти в JVM? Что будет с двумя или более объектами, которые ссылаются только друг на друга, но больше не на кого и никому не нужны - как с ними поступит сборщик и как именно это будет делать?

Память в Java делится на Stack и Heap.

Stack - это область памяти, доступ к которой организован в порядке LIFO. Сюда помещается frame - локальные переменные и параметры вызываемого метода. Здесь можно сразу уточнить, что примитивы хранятся на стеке, а вот у объектов тут хранится только ссылка, а сами объекты в Heap. НО, благодаря Escape Analysis и скаляризации из Java 6, объекты, которые являются исключительно локальными и не возвращаются за пределы выполняемого метода, также сохраняются в стеке.  Про Escape Analysis и скаляризацию есть доклад ([видео](https://www.youtube.com/watch?v=K6c3W6vhQOA) или [текст](https://habr.com/ru/company/jugru/blog/322348/)) Руслана Черемина, или ещё [тут](https://www.beyondjava.net/escape-analysis-java).

Frame создаётся и кладётся на Stack при вызове метода. Frame уничтожается, когда завершается его вызов метода, как в случае нормального завершения, так и в результате выброса неперехваченного исключения. У каждого потока есть свой Stack и он имеет ограниченный размер. Подробности можно посмотреть в [JVM Specification](https://docs.oracle.com/javase/specs/jvms/se13/html/jvms-2.html#jvms-2.5.5).

Теперь про Heap и сборку мусора. Тут большинство просто хочет услышать то, что написано в одном из [сообщений](https://t.me/seniorsITBlog/61) telegram-канала Senior's Blog. Процитирую основную часть здесь:

<blockquote>
Heap делится на два поколения:

<ol>
    <li>Young Generation
        <ol>
            <li>Eden</li>
            <li>Survivor 0 и Survivor 1</li>
        </ol>
    </li>
    <li>Old Generation
        <ol><li>Tenured</li></ol>
    </li>
</ol>

Young разделен на три части: Eden, Survivor 0 и Survivor 1. В Eden создаются все новые объекты. Один из Survivor регионов всегда пустой. При полном заполнении региона Eden запускается малая сборка мусора, и все живые объекты из Eden и Survivor перемещаются в пустой Survivor, а Eden и использующийся Survivor полностью очищается. Это делается для уменьшения фрагментации памяти. Объекты, которые несколько раз перемещаются между Survivor, затем помещаются в Tenured.

В случае, когда места для новых объектов не хватает уже в Tenured, в дело вступает полная сборка мусора, работающая с объектами из обоих поколений. При этом старшее поколение не делится на подрегионы по аналогии с младшим, а представляет собой один большой кусок памяти. Поэтому после удаления мертвых объектов из Tenured производится не перенос данных (переносить уже некуда), а их уплотнение, то есть размещение последовательно, без фрагментации. Такой механизм очистки называется Mark-Sweep-Compact по названию его шагов (пометить выжившие объекты, очистить память от мертвых объектов, уплотнить выжившие объекты).

Бывают еще объекты-акселераты, размер которых настолько велик, что создавать их в Eden, а потом таскать за собой по Survivor’ам слишком накладно. В этом случае они размещаются сразу в Tenured.

Младшее поколение занимает одну треть всей кучи, а старшее, соответственно, две трети. При этом каждый регион Survivor занимает одну десятую младшего поколения, то есть Eden занимает восемь десятых.
</blockquote>

Существуют следующие реализации GC:
- [Serial Garbage Collector](https://docs.oracle.com/en/java/javase/13/gctuning/available-collectors.html#GUID-45794DA6-AB96-4856-A96D-FDE5F7DEE498)
- [Parallel Garbage Collector](https://docs.oracle.com/en/java/javase/13/gctuning/parallel-collector1.html#GUID-DCDD6E46-0406-41D1-AB49-FB96A50EB9CE). По умолчанию в Java 8.
- [Concurrent Mark Sweep](https://docs.oracle.com/en/java/javase/13/gctuning/concurrent-mark-sweep-cms-collector.html#GUID-FF8150AC-73D9-4780-91DD-148E63FA1BFF) (CMS). Deprecated с Java 9.
- [Garbage-First](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html) (G1). [По умолчанию](http://openjdk.java.net/jeps/248) с Java 9. Есть [видео](https://www.youtube.com/watch?v=iGRfyhE02lA) от Владимира Иванова. Ещё можно почитать о G1 в [туториале](https://docs.oracle.com/en/java/javase/13/gctuning/garbage-first-garbage-collector.html#GUID-ED3AB6D3-FD9B-4447-9EDF-983ED2F7A573) по настройке от Oracle.
- [Z Garbage Collector](http://openjdk.java.net/jeps/333) (ZGC)
- [Shenandoah](https://wiki.openjdk.java.net/display/shenandoah/Main) Garbage Collector. Есть в наличии с Java 12. Тут, конечно же, нужно смотреть доклады Алексея Шипилёва - [раз](https://www.youtube.com/watch?v=JBaZ4lK6OBk), [два](https://www.youtube.com/watch?v=HBWaffsl7fo)

Если совсем кратко, то можно ознакомиться [тут](http://java-online.ru/garbage-collection.xhtml) и вот [тут](https://dzone.com/articles/jvm-architecture-explained).

Почитать на Хабре подробнее про сборку мусора в Java можно в серии статей "Дюк, вынеси мусор!" от @alygin - [раз](https://habr.com/ru/post/269621/), [два](https://habr.com/ru/post/269707/), [три](https://habr.com/ru/post/269863/).

Послушать про работу с памятью и сборщиках мусора можно в [выпуске 74](http://podlodka.io/74) подкаста Podlodka с Алексеем Шипилёвом в гостях. Обязательно загляните в полезные ссылки к выпуску.

Ещё можно вспомнить про:
- [Method Area](https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Method_Area) - область памяти с информацией о классах, включая статические поля. Одна на всю JVM.
- [Program Counter (PC) Register](https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Program_Counter_PC_Register) - отдельный на каждый поток регистр для хранения адреса текущей выполняемой инструкции.
- [Run-time Constant Pool](https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Java_Virtual_Machine_Stacks) - выделяется из Method Area для каждого класса или интерфейса. Грубо говоря, хранит литералы. [Подробнее](https://blog.jamesdbloom.com/JVMInternals.html#constant_pool).
- [Native Method Stack](https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Java_Virtual_Machine_Stacks) - собственно Stack для работы нативных методов.

Дополнительно про gc и саму JVM (ох, бохатая и животрепещущая тема):
- [На богомерзком medium](https://medium.com/datadriveninvestor/how-does-garbage-collection-work-in-java-da8f75ec6899) в картинках
- [Перевод](https://habr.com/ru/company/jugru/blog/443250/) [статьи](https://shipilev.net/jvm/diy-gc/) Алексея Шипилёва на Хабре - Самодельный сборщик мусора для OpenJDK
- [Отрывок](https://plumbr.io/handbook/garbage-collection-algorithms/marking-reachable-objects) из Java Garbage Collection Handbook про reachability algorithm
- [Статейка](https://en.wikipedia.org/wiki/Tracing_garbage_collection) на Википедии про Tracing garbage collection
- [Доклад](https://www.youtube.com/watch?v=e2lXj_t7ZBc) Simone Bordet про ZGC и Shenandoah
- [JVM Anatomy Quarks](https://shipilev.net/jvm/anatomy-quarks/) - серия постов от Алексея Шипилёва про устройство JVM. Это просто клад, за который будут воевать пришельцы на постапокалиптическую Землю, чтобы разгадать, как работает эта чёртва шайтан-виртуал-машина и промышленный код почивших человеков.

[к содержанию](#jvm)