[Оглавление](../README.md)

# Database

- [ACID - расшифровать и немного раскрыть](#acid---расшифровать-и-немного-раскрыть)
- [Уровни изолированности транзакции. Какую проблему решает каждый уровень? Какой по умолчанию в большинстве реляционных БД? Приходилось ли изменять уровень изолированности транзакции и для чего?](#уровни-изолированности-транзакции-какую-проблему-решает-каждый-уровень-какой-по-умолчанию-в-большинстве-реляционных-бд-приходилось-ли-изменять-уровень-изолированности-транзакции-и-для-чего)
- [Какие бывают типы индексов в БД и как они устроены под капотом? Что такое индекс вообще?](#какие-бывают-типы-индексов-в-бд-и-как-они-устроены-под-капотом-что-такое-индекс-вообще)
- [В чем разница между оптимистической и пессимистической блокировками?](#в-чем-разница-между-оптимистической-и-пессимистической-блокировками)

## ACID - расшифровать и немного раскрыть

- *A* - Атомарность (Atomicity). Гарантирует, что никакая транзакция не будет зафиксирована в системе частично.
- *C* - Согласованность (Consistency). Каждая успешная транзакция фиксирует только допустимые результаты.
- *I* - Изолированность (Isolation). Во время выполнения транзакции параллельные транзакции не должны оказывать влияния на её результат.
- *D* - Стойкость (Durability). Подтверждённые изменения не будут отменены (например, из-за сбоя).

[к содержанию](#database)

## Уровни изолированности транзакции. Какую проблему решает каждый уровень? Какой по умолчанию в большинстве реляционных БД? Приходилось ли изменять уровень изолированности транзакции и для чего?

Краткий конспект [статьи](https://ru.wikipedia.org/wiki/%D0%A3%D1%80%D0%BE%D0%B2%D0%B5%D0%BD%D1%8C_%D0%B8%D0%B7%D0%BE%D0%BB%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D0%BE%D1%81%D1%82%D0%B8_%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D0%B9) из Википедии:
- `Read uncommitted` (чтение незафиксированных данных). Он гарантирует только отсутствие потерянных обновлений (ситуация, когда при одновременном изменении одного блока данных разными транзакциями одно из изменений теряется).
- `Read committed` (чтение фиксированных данных). Именно он чаще всего является уровнем по умолчанию. Обеспечивает защиту от «грязного» чтения (чтение данных, добавленных или изменённых транзакцией, которая впоследствии откатится).
- `Repeatable read` (повторяемость чтения). Решает проблему неповторяющегося чтения (ситуация, когда при повторном чтении в рамках одной транзакции ранее прочитанные данные оказываются изменёнными).
- `Serializable` (упорядочиваемость). Транзакции полностью изолируются друг от друга, каждая выполняется так, как будто параллельных транзакций не существует - защищает от фантомного чтения (ситуация, когда при повторном чтении в рамках одной транзакции одна и та же выборка дает разные множества строк).

На Хабре есть несколько статей по теме на любой вкус:
- [Для самых маленьких](https://habr.com/ru/post/469415/)
- Для MSSQL: [раз](https://habr.com/ru/post/305600/), [два](https://habr.com/ru/company/infopulse/blog/261097/), [три](https://habr.com/ru/company/infopulse/blog/261101/)
- [Для PostgreSQL](https://habr.com/ru/post/317884/). Тут вообще стоит упомянуть для любителей углубиться в тему статьи из блога [Postgres Professional](https://habr.com/ru/company/postgrespro/), и в частности цикл статей @egorov по внутреннему устройству PostgreSQL (например, [раз](https://habr.com/ru/company/postgrespro/blog/442804/), [два](https://habr.com/ru/company/postgrespro/blog/462877/) и т.д.). А также на сайте Postgres Professional есть документация на русском языке и [страница](https://postgrespro.ru/docs/postgresql/12/transaction-iso) по данному вопросу.
- Для MySQL: [раз](https://habr.com/ru/post/135217/), [два](https://habr.com/ru/post/238513/)
- [Транзакции и механизмы их контроля](https://habr.com/ru/post/446662/)

[к содержанию](#database)

## Какие бывают типы индексов в БД и как они устроены под капотом? Что такое индекс вообще?

[Индекс БД](https://en.wikipedia.org/wiki/Database_index) - это структура данных, которая повышает скорость операций поиска данных в таблице БД за счет дополнительных операций записи и хранения для поддержания структуры данных индекса. Индексы используются для быстрого поиска данных без необходимости поиска каждой строки в таблице базы данных каждый раз при обращении к таблице БД. Ключом индекса выступает значение индексируемого столбца/столбцов. Некоторые БД позволяют строить индексы по выражениям.

В основе индексов чаще всего лежат следующие структуры данных:
- Семейство [B-Tree](https://en.wikipedia.org/wiki/B-tree)
- [HASH-таблицы](https://en.wikipedia.org/wiki/Hash_table)
- [Bitmap](http://citforum.ru/database/oracle/bitmap_index/)
- [Фильтр Блума](https://en.wikipedia.org/wiki/Bloom_filter)
- [Spatial grid](https://en.wikipedia.org/wiki/Grid_(spatial_index))
- [Quadtree](https://en.wikipedia.org/wiki/Quadtree)
- [R-Tree](https://en.wikipedia.org/wiki/Hilbert_R-tree)
- и другие.

[Классификацию индексов](http://tokarchuk.ru/2012/08/indexes-classification/) можно разделить по различным критериям. Следует уточнить или предложить интервьюеру какой именно критерий имеется в виду.
Например, по порядку сортировки:
- Упорядоченные - индексы, в которых записи упорядочены по возрастанию или убыванию. Реализуются на B-деревьях.
- Неупорядоченные - индексы, в которых записи неупорядоченны. Реализуются на хешах.
Или по воздействию на источник данных:
- Некластерные - такие индексы организуют ссылки на соответствующие строки и значения. Ссылка содержит информацию об ID файла, в котором хранится строка; ID страницы соответствующих данных; номер искомой строки на соответствующей странице; содержимое столбца. В таблице БД может быть несколько некластерных индексов.
- Кластерные - такие индексы представляют собой древовидную структуру данных, при которой значения индекса хранятся вместе с соответствующими им данными. Физическое расположение данных перестраивается в соответствии с порядком индекса, поэтому может быть только один такой индекс на таблицу БД.

Подробнее:
- [Обзор типов индексов Oracle, MySQL, PostgreSQL, MS SQL](https://habr.com/ru/post/102785/) на Хабре
- Великолепная серия статей от Егора Рогова про индексы в PostgreSQL: [часть 1](https://habr.com/ru/company/postgrespro/blog/326096/), [часть 2](https://habr.com/ru/company/postgrespro/blog/326106/), [часть 3](https://habr.com/ru/company/postgrespro/blog/328280/), [часть 4](https://habr.com/ru/company/postgrespro/blog/330544/), [часть 5](https://habr.com/ru/company/postgrespro/blog/333878/), [часть 6](https://habr.com/ru/company/postgrespro/blog/337502/), [часть 7](https://habr.com/ru/company/postgrespro/blog/340978/), [часть 8](https://habr.com/ru/company/postgrespro/blog/343488/), [часть 9](https://habr.com/ru/company/postgrespro/blog/346460/), [часть 10](https://habr.com/ru/company/postgrespro/blog/349224/)
- [Кластерные и «обычные» индексы MySQL (InnoDB)](https://habr.com/ru/post/141767/) на Хабре
- [«Под капотом» индексов Postgres](https://habr.com/ru/company/mailru/blog/261871/) на Хабре
- [Типы индексов, виды индексов, или какие вообще бывают индексы?](http://tokarchuk.ru/2012/08/indexes-classification/)
- [Индекс базы данных: типы индексов](https://alextoolsblog.blogspot.com/2019/11/database-index-types.html)
- [Индекс базы данных: методы индексирования, приложения и ограничения](https://alextoolsblog.blogspot.com/2019/11/database-index-methods.html)
- [Индекс базы данных](https://alextoolsblog.blogspot.com/2019/11/database-index.html)
- [Database Indexes Explained](https://www.essentialsql.com/what-is-a-database-index/)
- [Bitmap-индекс или B*tree-индекс: какой и когда применять?](http://citforum.ru/database/oracle/bb_indexes/)
- [Индексы в MySQL](https://ruhighload.com/%D0%98%D0%BD%D0%B4%D0%B5%D0%BA%D1%81%D1%8B+%D0%B2+mysql)
- [Как работает реляционная БД](https://habr.com/ru/company/mailru/blog/266811/) на Хабре
- [Здоровье индексов в PostgreSQL глазами Java-разработчика](https://habr.com/ru/post/490824/) на Хабре
- [14 вопросов об индексах в SQL Server, которые вы стеснялись задать](https://habr.com/ru/post/247373/) на Хабре
- [Вопросы про индексы, которые вам не надо будет задавать](https://habr.com/ru/post/247949/) на Хабре
- [Используем все возможности индексов в PostgreSQL](https://habr.com/ru/company/mailru/blog/453046/)
- [Anatomy of an SQL Index](https://use-the-index-luke.com/sql/anatomy)
- [SQL Indexing and Tuning e-Book](https://use-the-index-luke.com/)
- [Deep dive into Hash indexes for In-Memory OLTP tables](https://www.sqlshack.com/deep-dive-hash-indexes-memory-oltp-tables/#:~:text=Simply%20explained%2C%20in%20the%20In,in%20an%20in%2Dmemory%20table.)
- [Indexing based on Hashing](http://www.mathcs.emory.edu/~cheung/Courses/554/Syllabus/3-index/hashing.html)
- [SQL Server Indexes Interview Questions and Answers](https://dotnettutorials.net/lesson/sql-server-indexes-interview-questions-answers/)
- [Top 25 SQL interview questions and answers about indexes](https://www.sqlshack.com/top-25-sql-interview-questions-and-answers-about-indexes/)
- [An in-depth look at Database Indexin](https://www.freecodecamp.org/news/database-indexing-at-a-glance-bb50809d48bd/)

[к содержанию](#database)

## В чем разница между оптимистической и пессимистической блокировками?

[Блокировка](https://ru.wikipedia.org/wiki/%D0%91%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BA%D0%B0_(%D0%A1%D0%A3%D0%91%D0%94)) (lock) в СУБД — отметка о захвате объекта транзакцией в ограниченный или исключительный доступ с целью предотвращения коллизий и поддержания целостности данных.

Пессимистическая блокировка - накладывается перед предполагаемой модификацией данных на все строки, которые такая модификация предположительно затрагивает. Во время действия такой блокировки исключена модификация данных из сторонних сессий, данные из блокированных строк доступны согласно уровню изолированности транзакции. По завершению предполагаемой модификации гарантируется непротиворечивая запись результатов. Недостатком является то, что записи могут быть заблокированы на очень долгое время, тем самым замедляя общую реакцию системы.
Пессимистическая блокировка может быть двух типов:
- shared (read) - блокирует писателей, но позволяет читателям продолжить работу. Иными словами, разрешает другим транзакциям захватывать блокировку на чтение, но запрещает блокировку на запись.
- exclusive (write) - блокирует и писателей, и читателей. Делает все операции записи последовательными. Иными словами, запрещает другим транзакциям захватывать блокировки на чтение и на запись.

[Оптимистическая блокировка](https://en.wikipedia.org/wiki/Optimistic_concurrency_control) - не ограничивает модификацию обрабатываемых данных сторонними сессиями, однако перед началом предполагаемой модификации запрашивает значение некоторого выделенного атрибута каждой из строк данных (обычно используется наименование `VERSION` и целочисленный тип с начальным значением `0`). Перед записью модификаций в базу данных перепроверяется значение выделенного атрибута, и если оно изменилось, то транзакция откатывается или применяются различные схемы разрешения коллизий. Если значение выделенного атрибута не изменилось — производится фиксация модификаций с одновременным изменением значения выделенного атрибута (например, инкрементом) для сигнализации другим сессиям, что данные изменились.

Основное отличие в том, что оптимистическая блокировка приводит к накладным расходам только в случае конфликта, в то время как пессимистическая блокировка уменьшает накладные расходы на конфликт. Поэтому оптимистическая блокировка лучше всего подходит в случае, когда большинство транзакций не конфликтуют.

Много статей про блокировки с кодом и картинками можно прочитать в блоге Vlad Mihalcea:
- [A beginner’s guide to Java Persistence locking](https://vladmihalcea.com/a-beginners-guide-to-java-persistence-locking/)
- [A beginner’s guide to database locking and the lost update phenomena](https://vladmihalcea.com/a-beginners-guide-to-database-locking-and-the-lost-update-phenomena/)
- [How do LockModeType.PESSIMISTIC_READ and LockModeType.PESSIMISTIC_WRITE work in JPA and Hibernate](https://vladmihalcea.com/hibernate-locking-patterns-how-do-pessimistic_read-and-pessimistic_write-work/)
- [How does LockModeType.PESSIMISTIC_FORCE_INCREMENT work in JPA and Hibernate](https://vladmihalcea.com/hibernate-locking-patterns-how-does-pessimistic_force_increment-lock-mode-work/)
- [How does LockModeType.OPTIMISTIC work in JPA and Hibernate](https://vladmihalcea.com/hibernate-locking-patterns-how-does-optimistic-lock-mode-work/)
- [How does LockModeType.OPTIMISTIC_FORCE_INCREMENT work in JPA and Hibernate](https://vladmihalcea.com/hibernate-locking-patterns-how-does-optimistic_force_increment-lock-mode-work/)
- [How to prevent OptimisticLockException with Hibernate versionless optimistic locking](https://vladmihalcea.com/how-to-prevent-optimisticlockexception-using-hibernate-versionless-optimistic-locking/)
- [How does database pessimistic locking interact with INSERT, UPDATE, and DELETE SQL statements](https://vladmihalcea.com/how-does-database-pessimistic-locking-interact-with-insert-update-and-delete-sql-statements/)
- [How does MVCC (Multi-Version Concurrency Control) work](https://vladmihalcea.com/how-does-mvcc-multi-version-concurrency-control-work/)
- [How does the 2PL (Two-Phase Locking) algorithm work](https://vladmihalcea.com/2pl-two-phase-locking/)
- [How to prevent lost updates in long conversations](https://vladmihalcea.com/preventing-lost-updates-in-long-conversations/)

Подробнее на Хабре:
- [Конкурентный доступ к реляционным базам данных](https://habr.com/ru/post/121858/)
- [Базы данных. Конфликты параллельного доступа](https://habr.com/ru/post/86302/)
- [Замечательная аннотация Version в JPA](https://habr.com/ru/post/434836/)
- [Документация разработчика Hibernate – Глава V. Блокировки](https://habr.com/ru/post/268903/)
- [Блокировки в PostgreSQL: 1. Блокировки отношений](https://habr.com/ru/company/postgrespro/blog/462877/)
- [Блокировки в PostgreSQL: 2. Блокировки строк](https://habr.com/ru/company/postgrespro/blog/463819/)
- [Блокировки в PostgreSQL: 3. Блокировки других объектов](https://habr.com/ru/company/postgrespro/blog/465263/)
- [Блокировки в PostgreSQL: 4. Блокировки в памяти](https://habr.com/ru/company/postgrespro/blog/466199/)

Подробнее в других источниках:
- [Pessimistic Locking in JPA](https://www.baeldung.com/jpa-pessimistic-locking)
- [Optimistic Locking in JPA](https://www.baeldung.com/jpa-optimistic-locking)
- [Enabling Transaction Locks in Spring Data JPA](https://www.baeldung.com/java-jpa-transaction-locks)
- [Deadlock](https://en.wikipedia.org/wiki/Deadlock)
- [Enum LockModeType](https://docs.oracle.com/javaee/7/api/javax/persistence/LockModeType.html)
- [Явные блокировки в Postgresql](https://postgrespro.ru/docs/postgresql/12/explicit-locking)
- [PostgreSQL Concurrency with MVCC](https://devcenter.heroku.com/articles/postgresql-concurrency)
- [Locking](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#locking) в документации Hibernate
- [Optimistic locking](https://www.jooq.org/doc/latest/manual/sql-execution/crud-with-updatablerecords/optimistic-locking/) в документации JOOQ
- [Optimistically Locking Your Spring Boot Web Services](https://medium.com/slalom-build/optimistically-locking-your-spring-boot-web-services-187662eb8a91)
- [Optimistic and pessimistic locking with SQL](https://convincedcoder.com/2018/09/01/Optimistic-pessimistic-locking-sql/)

[к содержанию](#database)