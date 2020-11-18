[Оглавление](../README.md)

# Hibernate

- [Какие есть кэши в Hibernate и какие работают по умолчанию?](#какие-есть-кэши-в-hibernate-и-какие-работают-по-умолчанию)
- [Чем отличается Lazy от Eager в Hibernate?](#чем-отличается-lazy-от-eager-в-hibernate)
- [Что такое "проблема N+1 запроса" при использовании Hibernate? Когда возникает? Как решить? Как обнаружить?](#что-такое-проблема-n1-запроса-при-использовании-hibernate-когда-возникает-как-решить-как-обнаружить)
- [Как описать составной ключ при использовании Hibernate?](#как-описать-составной-ключ-при-использовании-hibernate)
- [Как можно отобразить наследование на БД с помощью JPA (Hibernate)?](#как-можно-отобразить-наследование-на-бд-с-помощью-jpa-hibernate)

## Какие есть кэши в Hibernate и какие работают по умолчанию?

3 уровня кеширования:
- Кеш первого уровня (First-level cache). По умолчанию включен.
- Кеш второго уровня (Second-level cache). По умолчанию отключен.
- Кеш запросов (Query cache). По умолчанию отключен.

Подробнее:
- [Статья](https://habr.com/ru/post/135176/) на Хабре
- [Документация](https://docs.jboss.org/hibernate/stable/orm/userguide/html_single/Hibernate_User_Guide.html#caching) и на Хабре есть [перевод](https://habr.com/ru/post/268747/)
- [Статья](https://www.baeldung.com/hibernate-second-level-cache) на Baeldung
- Статьи с практическими примерами о [First Level Cache](https://howtodoinjava.com/hibernate/understanding-hibernate-first-level-cache-with-example/) и [Second Level Cache](https://howtodoinjava.com/hibernate/how-hibernate-second-level-cache-works/)

Углубиться в Hibernate нам всегда поможет [Vlad Mihalcea](https://vladmihalcea.com/):
- Конечно же в его книге [High-Performance Java Persistence](https://vladmihalcea.teachable.com/p/high-performance-java-persistence-ebook/?utm_source=blog&utm_medium=banner&utm_campaign=banner): Часть 2, глава 16 - [Caching](https://vladmihalcea.com/high-performance-java-persistence-chapter-16-caching/)
- [How does Hibernate Query Cache work](https://vladmihalcea.com/how-does-hibernate-query-cache-work/)
- [How does Hibernate Collection Cache work](https://vladmihalcea.com/how-does-hibernate-collection-cache-work/)
- [How does Hibernate store second-level cache entries](https://vladmihalcea.com/how-does-hibernate-store-second-level-cache-entries/)
- [How does Hibernate NONSTRICT_READ_WRITE CacheConcurrencyStrategy work](https://vladmihalcea.com/how-does-hibernate-nonstrict_read_write-cacheconcurrencystrategy-work/)
- [How does Hibernate TRANSACTIONAL CacheConcurrencyStrategy work](https://vladmihalcea.com/how-does-hibernate-transactional-cacheconcurrencystrategy-work/)
- [How does Hibernate READ_ONLY CacheConcurrencyStrategy work](https://vladmihalcea.com/how-does-hibernate-read_only-cacheconcurrencystrategy-work/)
- [How does Hibernate READ_WRITE CacheConcurrencyStrategy work](https://vladmihalcea.com/how-does-hibernate-read_write-cacheconcurrencystrategy-work/)

[к содержанию](#hibernate)

## Чем отличается Lazy от Eager в Hibernate?

- [Eager Loading](https://docs.oracle.com/javaee/7/api/javax/persistence/FetchType.html#EAGER) - стратегия загрузки, при которой подгрузка связанных сущностей происходит сразу. Для применения необходимо в аннотацию отношения (`@OneToOne`, `@ManyToOne`, `@OneToMany`, `@ManyToMany`) передать `fetch = FetchType.EAGER`. Используется по умолчанию для отношений `@OneToOne` и `@ManyToOne`.
- [Lazy Loading](https://docs.oracle.com/javaee/7/api/javax/persistence/FetchType.html#LAZY) - стратегия загрузки, при которой подгрузка связанных сущностей откладывается как можно дольше. Чтобы задать такое поведение, нужно в аннотацию отношения (`@OneToOne`, `@ManyToOne`, `@OneToMany`, `@ManyToMany`) передать `fetch = FetchType.LAZY`. Используется по умолчанию для отношений `@OneToMany`, `@ManyToMany`. До момента загрузки используется proxy-объект, вместо реального. Если обратиться к такому LAZY-полю после закрытия сессии Hibernate, то получим [LazyInitializationException](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/LazyInitializationException.html).

Вопрос также связан с проблемой "N+1" и может плавно перетечь в её обсуждение.

Почитать подробнее и с примерами можно в блоге Vlad Mihalcea: [раз](https://vladmihalcea.com/hibernate-facts-the-importance-of-fetch-strategy/), [два](https://vladmihalcea.com/eager-fetching-is-a-code-smell/) и про LazyInitializationException [три](https://vladmihalcea.com/the-best-way-to-handle-the-lazyinitializationexception/).

[к содержанию](#hibernate)

## Что такое "проблема N+1 запроса" при использовании Hibernate? Когда возникает? Как решить? Как обнаружить?

Проблема N+1 может возникнуть не только при использовании Hibernate, но и других библиотек и фреймворков для доступа к данным.

В общем случае говорят о проблеме N+1 запроса, когда фреймворк выполняет N дополнительных запросов выборки данных, когда можно было обойтись всего одним. Соответственно от размера N зависит влияние проблемы на время ответа нашего приложения. Эту ситуацию нельзя обнаружить с помощью `slow query log`, ибо сами по себе запросы могут выполняться быстро, но их количество окажется большим или даже огромным.

На такое можно нарваться даже при использовании plain sql (jdbc, JOOQ), когда у нас одна сущность (и соответственно таблица) связана с другой. И вот мы подгрузили одним запросом просто список из первых, а потом пошли и в цикле для каждой подгрузили связанную по одному запросу. "Да как вы это допустили!?". Да просто по запарке кто-то в цикле начал вызывать метод, у которого в глубине где-то делается запрос и привет. Как исправить? Использовать `JOIN` со связанной таблицей при чтении списка. Тогда понадобиться лишь один запрос.

Теперь к Hibernate. Если на странице [документации](https://docs.jboss.org/hibernate/stable/core/userguide/html_single/Hibernate_User_Guide.html) поискать "N+1", то можно обнаружить несколько упоминаний данной проблемы. Тут опишу самые явные и распространённые.

Например, возьмём стратегию выборки `FetchType.EAGER`. Она склонна к порождению N+1. А в отношении `@ManyToOne` по умолчанию используется именно она. Забыли в своём JPQL запросе заиспользовать `JOIN FETCH` и привет. А если нам и не нужны были связанные сущности, то тогда стоит задать стратегию `FetchType.LAZY`.

Если уж упомянули `FetchType.LAZY`, то сразу стоит сказать, что одно её наличие не гарантирует отсутствие проблемы N+1. При выборке списка сущностей, связанные автоматически не подгрузились. А мы потом пошли в цикле по загруженному списку и стали обращаться к полям связанной сущности - и снова здравствуйте. Всё тот же `JOIN FETCH` нас спасёт и в этой ситуации.

Но `JOIN FETCH` во многих случаях нас может привести к декартовому произведению, и тогда будет совсем bonjour. Для отношения `@OneToMany` это можно решить с помощью `FetchMode.SUBSELECT` - будет 2 запроса, но во втором запросе на получение списка связанных сущностей в условии выборки будет подзапрос на получение идентификаторов родительских сущностей. Т.е. запрос практически повторяется и он может быть тяжеловесным.

Есть вариант лучше - вычитывать связанные сущности пачками. Мы можем добавить аннотацию `@BatchSize` и указать размер подгружаемой пачки записей в одном запросе.

Ещё варианты:
- [JPA EntityGraph](https://www.baeldung.com/jpa-entity-graph)
- [Hibernate FetchProfile](https://docs.jboss.org/hibernate/stable/core/userguide/html_single/Hibernate_User_Guide.html#fetching-strategies-dynamic-fetching-profile-example)

Чтобы обнаружить проблему N+1, нужно писать тесты с использованием библиотеки [db-util](https://github.com/vladmihalcea/db-util) от Vlad Mihalcea. Подробнее можно прочитать у него же в [блоге](https://vladmihalcea.com/how-to-detect-the-n-plus-one-query-problem-during-testing/).

А вот JOOQ умеет обнаруживать N+1 автоматически, послушать об этом можно в [17-м эпизоде](https://it.asm0dey.ru/podcast/%d0%bf%d0%b0%d1%88%d0%b0-%d1%81%d0%bb%d0%b0%d0%b2%d0%b0-%d1%8d%d0%bf%d0%b8%d0%b7%d0%be%d0%b4-17/) (01:16:36) подкаста Паша+Слава. Подробнее в [документации](https://www.jooq.org/doc/3.11/manual-single-page/#diagnostics-repeated-statements) JOOQ.

Углубиться в проблему можно:
- В блоге Vlad Mihalcea: [раз](https://vladmihalcea.com/n-plus-1-query-problem/) и [два](https://vladmihalcea.com/hibernate-query-cache-n-plus-1-issue/)
- На хабре упоминалась в статье [Hibernate — о чем молчат туториалы](https://habr.com/ru/post/416851/)
- На DOU про стратегии загрузки коллекций в [JPA](https://dou.ua/lenta/articles/jpa-fetch-types/) и [Hibernate](https://dou.ua/lenta/articles/hibernate-fetch-types/)
- В видео формате в докладе Николая Алименкова на JPoint ["Сделаем Hibernate снова быстрым"](https://youtu.be/b52Qz6qlic0?t=1224)

[к содержанию](#hibernate)

## Как описать составной ключ при использовании Hibernate?

На всякий случай: `составной ключ` - первичный ключ, состоящий из двух и более атрибутов. Вообще про ключи есть большая [статья](https://habr.com/ru/company/oleg-bunin/blog/348172/) на Хабре с ценными комментариями.

Чтобы описать составной ключ при использовании Hibernate, нам необходимо создать под этот ключ отдельный класс с необходимыми полями и добавить ему аннотацию `@Embeddable`. Кроме того, он должен быть `Serializable` и иметь реализацию `equals` и `hashcode`.

В самой же сущности, для которой мы описываем составной ключ, добавляем поле только что созданного класса ключа и вешаем на него аннотацию `@EmbeddedId`.

Посмотреть примеры и углубиться в тему можно в [статье](https://vladmihalcea.com/the-best-way-to-map-a-composite-primary-key-with-jpa-and-hibernate/) Vlad Mihalcea или в [документации](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#identifiers-composite-aggregated) Hibernate.

[к содержанию](#hibernate)

## Как можно отобразить наследование на БД с помощью JPA (Hibernate)?

Есть 4 способа отобразить наследование на БД с помощью JPA (Hibernate):
- [MappedSuperclass](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#entity-inheritance-mapped-superclass) - поля родителя содержатся в каждой таблице для каждого дочернего класса. Базовый класс отдельной таблицы не имеет. На базовый класс навешиваем [@MappedSuperClass](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/MappedSuperclass.html), а вот на дочерние `@Entity`. Если в таблице потомка поле родителя называется не так, как указано в родительском классе, то его нужно смаппить с помощью аннотации [@AttributeOverride](https://docs.jboss.org/hibernate/jpa/2.1/api/javax/persistence/AttributeOverride.html) в классе этого потомка. Родитель не может участвовать в ассоциации. При полиморфных запросах у нас будут отдельные запросы для каждой таблицы.
- [Single table](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#entity-inheritance-single-table) - вся иерархия классов в одной таблице. Чтобы различать классы, необходимо добавить колонку-дискриминатор. В данной стратегии на родительский `@Entity`-класс навешивается `@Inheritance(strategy = InheritanceType.SINGLE_TABLE)` и `@DiscriminatorColumn(name = "YOUR_DISCRIMINATOR_COLUMN_NAME")` (по умолчанию имя колонки `DTYPE` и тип `VARCHAR`). В каждом подклассе указываем `@DiscriminatorValue("ThisChildName")` со значением, которое будет храниться в колонке-дискриминаторе для данного класса. Если нет возможности добавить колонку, то можно использовать аннотацию [@DiscriminatorFormula](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/annotations/DiscriminatorFormula.html), в которой указать выражение `CASE...WHEN` - это не по JPA, фишка Hibernate. Денормализация. Простые запросы к одной таблице. Возможное нарушение целостности - столбцы подклассов могут содержать `NULL`.
- [Joined table](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#entity-inheritance-joined-table) - отдельные таблицы для всех классов иерархии, включая родителя. В каждой таблице только свои поля, а в дочерних добавляется внешний (он же первичный) ключ для связи с родительской таблицей. В `@Entity`-класс родителя добавляем `@Inheritance(strategy = InheritanceType.JOINED)`. Для полиморфных запросов используются `JOIN`, а также выражение `CASE...WHEN`, вычисляющее значение поля `_clazz`, которое заполняется литералами (0 (родитель), 1, 2 и т.д.) и помогает Hibernate определить какого класса будет экземпляр.
- [Table per class](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#entity-inheritance-table-per-class) - также как и в `MappedSuperclass`, имеем отдельные таблицы для каждого подкласса. Базовый класс отдельной таблицы не имеет. По [спецификации](https://download.oracle.com/otn-pub/jcp/persistence-2_2-mrel-spec/JavaPersistence.pdf) JPA 2.2 (раздел 2.12) данная стратегия является опциональной, но в Hibernate реализована, поэтому продолжим. В данном случае на базовый класс мы навешиваем `@Entity` и `@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)`. Поле первичного ключа (`@Id`) обязательно для родительского класса. Также аннотация `@AttributeOverride` в этой стратегии не работает - называйте родительские поля в таблицах сразу единообразно. Полиморфный запрос будет использовать `UNION` для объединения таблиц. Чтобы различить при создании экземпляров подклассы, Hibernate добавляет поле `_clazz` в запросы, содержащие литералы (1, 2 и т.д.). А одинаковый набор столбцов для объединения добирается как `NULL AS some_field`. Родитель может участвовать в ассоциации с другими сущностями.

Почитать ещё по теме с примерами можно:
- На Хабре: [раз](https://habr.com/ru/post/337488/), [два](https://habr.com/ru/post/416851/)
- В блоге Vlad Mahalcea: [Влад демонстрирует лучшую стратегию](https://vladmihalcea.com/the-best-way-to-use-entity-inheritance-with-jpa-and-hibernate/) и [рассказывает про MappedSuperclass](https://vladmihalcea.com/how-to-inherit-properties-from-a-base-class-entity-using-mappedsuperclass-with-jpa-and-hibernate/)

Кроме того, можно заглянуть в JavaDoc с аннотациями:
- [JPA](https://docs.jboss.org/hibernate/jpa/2.2/api/javax/persistence/package-summary.html)
- [Hibernate](https://docs.jboss.org/hibernate/orm/5.4/javadocs/org/hibernate/annotations/package-summary.html)

Или почитать спецификацию [JPA](https://download.oracle.com/otn-pub/jcp/persistence-2_2-mrel-spec/JavaPersistence.pdf) 2.2. Ещё есть [книжка](https://www.amazon.com/Understanding-JPA-2-2-Persistence-fascicle-ebook/dp/B07RWPXPS6) с толкованием данного pdf.

[к содержанию](#hibernate)