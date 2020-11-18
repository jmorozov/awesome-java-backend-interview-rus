[Оглавление](../README.md)

# Сопровождение, поддержка, эксплуатация

- [Могут ли быть в Java утечки памяти и когда? Как обнаружить причину? Как снять heap-dump?](#могут-ли-быть-в-java-утечки-памяти-и-когда-как-обнаружить-причину-как-снять-heap-dump)
- [Что такое Xmx и Xms, Xss?](#что-такое-xmx-и-xms-xss)
- [Какие инструменты для нагрузочного тестирования вы знаете?](#какие-инструменты-для-нагрузочного-тестирования-вы-знаете)
- [Чем докер отличается от виртуалки? Что лежит в его основе? Как работает Docker?](#чем-докер-отличается-от-виртуалки-что-лежит-в-его-основе-как-работает-docker)
- [Чем контейнер сервлетов отличается от сервера приложений? Какие знаете?](#чем-контейнер-сервлетов-отличается-от-сервера-приложений-какие-знаете)

## Могут ли быть в Java утечки памяти и когда? Как обнаружить причину? Как снять heap-dump?

Могут. Профилировать. Снимать heap-dump, например с помощью [jmap](https://docs.oracle.com/en/java/javase/14/docs/specs/man/jmap.html), загружать в memory profiler (например в [VisualVM](https://visualvm.github.io/))

Подробнее:
- Доступно изложено на [Baeldung](https://www.baeldung.com/java-memory-leaks) или то же самое [тут](https://topjava.ru/blog/java-memory-leaks), но на языке родных осин.
- Ещё [тут](https://www.toptal.com/java/hunting-memory-leaks-in-java)
- [здесь](https://dzone.com/articles/memory-leak-andjava-code)
- Старенькая [статья](https://habr.com/ru/post/132500/) на Хабре про типичные случаи утечки памяти в Java
- [Диагностика утечек памяти в Java](https://habr.com/ru/post/324144/) на Хабре
- [Ищем утечки памяти с помощью Eclipse MAT](https://habr.com/ru/post/519830/) на Хабре
- [Устранение утечек памяти посредством слабых ссылок](https://www.ibm.com/developerworks/ru/library/j-jtp11225/index.html)
- [Устранение утечек памяти посредством гибких ссылок](https://www.ibm.com/developerworks/ru/library/j-jtp01246/index.html)
- [Бывают ли в Java утечки памяти?](https://itsobes.ru/JavaSobes/byvaiut-li-v-java-utechki-pamiati/)
- [Диагностика OutOfMemoryError подручными средствами](http://bazhenov.me/blog/2009/12/20/out-of-memory-error.html)
- [Java VisualVM - Browsing a Heap Dump](https://docs.oracle.com/javase/8/docs/technotes/guides/visualvm/heapdump.html)
- [VisualVM: мониторинг, профилировка и диагностика Java-приложений](https://shipilev.net/blog/archive/visualvm/)
- Доклад Андрея Паньгина [Всё, что вы хотели знать о стек-трейсах и хип-дампах](https://www.youtube.com/watch?v=0pyZERLBZvQ&feature=emb_logo)
- [Different Ways to Capture Java Heap Dumps](https://www.baeldung.com/java-heap-dump-capture)
- [Analyze memory snapshots](https://www.jetbrains.com/help/idea/analyze-hprof-memory-snapshots.html) с помощью IntelliJ IDEA
- [Analyze objects in the JVM heap](https://www.jetbrains.com/help/idea/analyze-objects-in-the-jvm-heap.html) с помощью IntelliJ IDEA

[к содержанию](#сопровождение-поддержка-эксплуатация)

## Что такое Xmx и Xms, Xss?

JVM стартует с `Xms` количеством выделенной под heap памяти и максимально может увеличить её до значения `Xmx`.
`Xss` флаг определяет размер выделенной под стек памяти.

Общий вид:
```
java -Xmx<количество><единица измерения>
```

Можно использовать различные единицы измерения, например килобайты (`k`), мегабайты (`m`) или гигабайты (`g`).

Пример:
```bash
java -jar my.jar -Xms256m -Xmx2048m
```

Подробнее:
- В [статье](https://mkyong.com/java/java-what-is-xms-and-xmx-parameter/) на Mkyong
- В [статье](https://docs.oracle.com/cd/E21764_01/web.1111/e13814/jvm_tuning.htm#PERFM150) Tuning JVM от Orcale
- В [статье](https://docs.oracle.com/en/java/javase/14/gctuning/factors-affecting-garbage-collection-performance.html) Factors Affecting Garbage Collection Performance от Oracle
- [Список](https://docs.oracle.com/cd/E13150_01/jrockit_jvm/jrockit/jrdocs/refman/optionX.html) всех X опций CLI

[к содержанию](#сопровождение-поддержка-эксплуатация)

## Какие инструменты для нагрузочного тестирования вы знаете?

Благодаря нагрузочному тестированию появляется возможность оценить производительность приложения при различных нагрузках от действий определённого количества пользователей приложения.

Существует несколько решений для проведения подобного рода тестирования:
- [Apache JMeter](https://jmeter.apache.org/) - самый известный, opensource, поддерживает работу с различными сетевыми протоколами разных уровней HTTP, HTTPS, FTP, LDAP, SOAP, TCP, почтовых протоколов и shell-скриптов. Он, как java-based инструмент, предоставляет возможности по работе с JDBC, Message-oriented middleware (MOM) через JMS и Java Objects.
- [LoadRunner](https://www.microfocus.com/en-us/products/loadrunner-professional/overview) - это сложный и универсальный инструмент для нагрузочного тестирования различных платформ. Платный, ориентирован в первую очередь на Enterprise.
- [LoadNinja](https://loadninja.com/) - платный, позволяет пользователям создавать нагрузочные тесты без использования каких-либо скриптов. Средствами браузера собирает метрики, которые позволяют оценить производительность приложения.
- [WebLOAD](https://www.radview.com/) - коммерческий комплексный инструмент. Предоставляет пользователям расширенные функции, такие как анализ производительности и интеллектуальная аналитика, а также интеграцию с рядом инструментов, начиная от мониторинга производительности до конвейеров CI/CD.
- [LoadUI Pro](https://www.soapui.org/tools/loadui-pro/) - коммерческий инструмент от создателей [SOAP UI](https://www.soapui.org/), тесты которого можно использовать как сценарии нагрузочного тестирования. Предназначен для веб-сервисов и позволяет пользователям оценивать масштабируемость, скорость и производительность API.
- [K6](https://k6.io/) - opensource инструмент, ориентированный на разработчиков и тестирования производительности сайтов. Скрипты пишутся на JavaScript. Запуск тестов происходит в консольном режиме, результаты тестирования по умолчанию также выводятся в консоль, однако доступна поддержка таких плагинов для вывода результатов, как Kafka, Datadog, InfluxDB, JSON и StatsD. Доступна интеграция с CI-инструментами.
- [Яндекс.Танк](https://yandex.ru/dev/tank/) - инструмент от Яндекс с модульной архитектурой, которая позволяет использовать различные генераторы нагрузок -  высокопроизводительный асинхронный hit-based-генератор HTTP-запросов Phantom и сценарный инструмент jMeter. Встроенный мониторинг ресурсов, автоматическая остановка теста по заданным условиям, вывод результатов в консоль и в виде графиков, подключение пользовательских модулей.
- [Gatling](https://gatling.io/) - opensource инструмент на Scala с использованием [Netty](https://netty.io/) и [Akka](https://akka.io/). Скрипты пишутся на Scala, есть возможность интеграции с [Jenkins](https://www.jenkins.io/).

Подробнее:
- [Обзор инструментария для нагрузочного и перформанс-тестирования](https://habr.com/ru/company/jugru/blog/337928/) на Хабре
- [Нагрузочное тестирование: с чего начать и куда смотреть](https://habr.com/ru/company/jugru/blog/329174/) на Хабре
- [Нагрузочное тестирование на Gatling](https://habr.com/ru/company/tinkoff/blog/323316/) на Хабре
- [Top 10 лучших инструментов для нагрузочного тестирования](https://www.performance-lab.ru/blog/luchshie-instrumenty-dlya-nagruzochnogo-testirovaniya)
- [9 этапов тестирования производительности](https://www.a1qa.ru/blog/9-etapov-testirovaniya-proizvoditelnosti/)
- [10 шагов для запуска тестирования производительности с Apache JMeter](https://medium.com/effective-developers/10-steps-to-run-first-performance-test-with-apache-jmeter-52867c12b0a4)
- [jMeter — Краткое руководство](https://coderlessons.com/tutorials/java-tekhnologii/vyuchi-jmeter/jmeter-kratkoe-rukovodstvo)
- [Rest API Load testing with Apache JMeter](https://medium.com/@chamikakasun/rest-api-load-testing-with-apache-jmeter-a4d25ea2b7b6)
- [Load Testing with Gatling - The Complete Guide](https://www.james-willett.com/gatling-load-testing-complete-guide/)
- [Открытые бенчмарки для нагрузочного тестирования серверов и веб-приложений](https://habr.com/ru/company/1cloud/blog/474474/)

[к содержанию](#сопровождение-поддержка-эксплуатация)

## Чем докер отличается от виртуалки? Что лежит в его основе? Как работает Docker?

[Виртуальная машина](https://en.wikipedia.org/wiki/Virtual_machine) (Virtual Machine - VM) - программная и/или аппаратная система, эмулирующая аппаратное обеспечение и исполняющее приложения некоторой целевой платформы на платформе хоста. Иными словами - это виртуальный компьютер с виртуальными устройствами и независимой операционной системой, управлением памятью и другими компонентами. Т.о. на одном реальном компьютере может существовать несколько виртуальных, изолированных друг от друга, со своими ОС. Запускаемые внутри виртуальной машины приложения не имеют понятия о её виртуальности и взаимодействуют с ней как с реальной. Хотя VM изолирована от реального хоста, она может иметь доступ к его диску и периферийным устройствам. Существует возможность сделать backup'ы VM в виде текущего состояния системы и содержимого дисков для возможности последующего восстановления системы в исходное состояние.

Каждая VM несёт дополнительные расходы на эмуляцию оборудования и работу своей ОС.

Управляет виртуальными машинами на реальном железе [гипервизор](https://en.wikipedia.org/wiki/Hypervisor) - это программное и/или аппаратное решение, процесс, который отделяет операционную систему компьютера и приложения от базового физического оборудования.

Самими популярными решениями виртуализации являются [KVM](https://www.linux-kvm.org/page/Main_Page) с [QEMU](https://www.qemu.org/), [XEN](https://xenproject.org/), решения от [VMWare](https://www.vmware.com/) и [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/).

[Docker](https://www.docker.com/get-started) - программное решение, использующее [виртуализацию на уровне операционной системы](https://en.wikipedia.org/wiki/OS-level_virtualization) для доставки, развёртывания, изоляции и упаковки приложения со всеми его зависимостями в т.н. [контейнер](https://www.docker.com/resources/what-container).

Контейнеры расходуют меньше места на диске и ресурсов хоста, потому что переиспользуют большее количество общих ресурсов хоста и обеспечивает виртуализацию на уровне ОС. Каждый из контейнеров работает как отдельный процесс основной ОС, у которого есть своё собственное виртуальное адресное пространство. Изоляция контейнеров достигается с помощью [linux namespaces](https://en.wikipedia.org/wiki/Linux_namespaces). Ограничить потребление системных ресурсов контейнером можно через [cgroups](https://en.wikipedia.org/wiki/Cgroups).

Контейнер создаётся из образа, который в свою очередь основан на `Dockerfile` проекта, и представляет собой набор файловых систем (слоёв) наслоённых друг на друга и сгруппированных вместе, доступных только для чтения. Контейнеры всегда создаются из образов, добавляя свой собственный доступный для записи верхний слой, в который вносятся необходимые для контейнера в процессе работы изменения. Это позволяет нескольким контейнерам переиспользовать один образ.

Docker-контейнеры используются различными системами оркестрации и CI/CD.

У Docker есть большой репозиторий образов - [Docker-Hub](https://www.docker.com/products/docker-hub)

Кроме Docker, существуют и [другие контейнеры](https://en.wikipedia.org/wiki/List_of_Linux_containers).

На Хабре есть множество статей на тему Docker:
- [VM или Docker?](https://habr.com/ru/post/474068/)
- Серия статей "Изучаем Docker": [част 1](https://habr.com/ru/company/ruvds/blog/438796/), [часть 2](https://habr.com/ru/company/ruvds/blog/439978/), [часть 3](https://habr.com/ru/company/ruvds/blog/439980/), [часть 4](https://habr.com/ru/company/ruvds/blog/440658/), [часть 5](https://habr.com/ru/company/ruvds/blog/440660/), [часть 6](https://habr.com/ru/company/ruvds/blog/441574/)
- [Docker. Начало](https://habr.com/ru/post/353238/)
- [Как устроен процесс создания docker-контейнера (от docker run до runc)](https://habr.com/ru/company/otus/blog/511414/)
- [Полное практическое руководство по Docker: с нуля до кластера на AWS](https://habr.com/ru/post/310460/)
- [Лабораторная работа: введение в Docker с нуля. Ваш первый микросервис](https://habr.com/ru/post/346634/)
- [Исповедь docker хейтера](https://habr.com/ru/post/467607/)
- [Глубокое погружение в Linux namespaces](https://habr.com/ru/post/458462/)
- [Как я запускал Докер внутри Докера и что из этого получилось](https://habr.com/ru/post/477464/)
- [Понимая Docker](https://habr.com/ru/post/253877/)
- [Поняв Docker](https://habr.com/ru/post/277699/)
- [Основы Docker за Х часов и Y дней](https://habr.com/ru/post/337306/)
- [Docker и все, все, все](https://habr.com/ru/company/southbridge/blog/512246/)
- [Безопасность для Docker-контейнеров](https://habr.com/ru/company/flant/blog/474012/)
- [Методики уменьшения размеров образов Docker](https://habr.com/ru/company/ruvds/blog/485650/)
- 10 практических рекомендаций по безопасности образов Docker: [часть 1](https://habr.com/ru/company/otus/blog/480970/), [часть 2](https://habr.com/ru/company/otus/blog/482044/)
- [Docker: вредные советы](https://habr.com/ru/company/southbridge/blog/449944/)
- [Docker: невредные советы](https://habr.com/ru/company/southbridge/blog/452108/)
- [Зачем нужен containerd и почему его отделили от Docker](https://habr.com/ru/company/flant/blog/325358/)
- [Зрелая исполняемая среда для контейнеров: containerd стал "выпускником" CNCF](https://habr.com/ru/company/flant/blog/442036/)

А также на тему виртуализации:
- [Гипервизоры. Что же это и как работает виртуальный сервер?](https://habr.com/ru/company/vps_house/blog/349788/)
- [Автоматизация Для Самых Маленьких. Часть 1.1. Основы виртуализации](https://habr.com/ru/post/467801/)
- [Архитектура Hyper-V: Глубокое погружение](https://habr.com/ru/post/98580/)
- [Hyper-V для разработчиков под Windows 10](https://habr.com/ru/company/microsoft/blog/481248/)
- [Общие принципы работы QEMU-KVM](https://habr.com/ru/post/466549/)
- [Работа с виртуальными машинами KVM. Введение](https://habr.com/ru/post/120432/)
- [Показатели эффективности: KVM vs. Xen](https://habr.com/ru/company/cloud4y/blog/282918/)
- [Hyper-V или KVM?](https://habr.com/ru/company/ruvds/blog/308350/)

Кроме того, можно углубиться в других источниках:
- [Containers vs. virtual machines](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/containers-vs-vm)
- [Play with Docker](https://www.docker.com/play-with-docker)
- [Документация Docker](https://docs.docker.com/get-started/overview/)
- [The Docker EcoSystem](https://legacy.gitbook.com/book/washraf/the-docker-ecosystem/details)
- [Hardware-assisted virtualization](https://en.wikipedia.org/wiki/Hardware-assisted_virtualization)
- [Full virtualization](https://en.wikipedia.org/wiki/Full_virtualization)
- [Сравнение гипервизоров: KVM, Hyper-V или VMware?](https://www.xelent.ru/blog/sravnenie-gipervizorov-kvm-hyper-v-ili-vmware/)
- [Вся правда о гипервизорах](https://www.sim-networks.com/blog/hypervisors-vmware-kvm-xen-openvz)
- [Перестаем бояться виртуализации при помощи KVM](https://eax.me/kvm/)
- [Основы виртуализации и введение в KVM](https://mkdev.me/posts/osnovy-virtualizatsii-i-vvedenie-v-kvm)
- [Виртуализация в Unix. Часть 1: Kernel-based Virtual Machine (KVM)](https://medium.com/@alexander.bazhenov/%D0%B2%D0%B8%D1%80%D1%82%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F-%D0%B2-linux-%D1%87%D0%B0%D1%81%D1%82%D1%8C-1-kernel-based-virtual-machine-kvm-55f989880dbf)
- [Настольная книга по Linux/Cgroups](https://ru.wikibooks.org/wiki/%D0%9D%D0%B0%D1%81%D1%82%D0%BE%D0%BB%D1%8C%D0%BD%D0%B0%D1%8F_%D0%BA%D0%BD%D0%B8%D0%B3%D0%B0_%D0%BF%D0%BE_Linux/Cgroups)
- [Механизмы контейнеризации: cgroups](https://selectel.ru/blog/mexanizmy-kontejnerizacii-cgroups/)
- [Введение в контейнеры, VM и Docker для новичков](http://falbar.ru/article/vvedenie-v-kontejnery-vm-i-docker-dlya-novichkov)
- [containerd](https://containerd.io/)
- [awesome-compose](https://github.com/docker/awesome-compose)
- [Learn Docker & Containers using Interactive Browser-Based Scenarios](https://www.katacoda.com/courses/docker)
- [What Is Docker & Docker Container? A Deep Dive Into Docker!](https://www.edureka.co/blog/what-is-docker-container)
- [Docker for beginners](https://docker-curriculum.com/)

[к содержанию](#сопровождение-поддержка-эксплуатация)

## Чем контейнер сервлетов отличается от сервера приложений? Какие знаете?

[Контейнер сервлетов](https://en.wikipedia.org/wiki/Web_container) - это компонент веб-сервера для взаимодействия с [Java-сервлетами](https://en.wikipedia.org/wiki/Java_servlet) - обеспечивает их жизненный цикл, сопоставление URL-адреса к определённому сервлету и имеет ли отправитель запроса по данному URL необходимые права доступа и т.п.

Примеры (также являются самостоятельными веб-серверами, но НЕ серверами приложений (реализуют неполный стек Java EE)):
- Самый известный - [Apache Tomcat](https://en.wikipedia.org/wiki/Apache_Tomcat). Есть несколько вариаций: от простого контейнера до полноценного сервера приложений (TomEE)
- [Jetty](https://en.wikipedia.org/wiki/Jetty_(web_server))
- [Undertow](http://undertow.io/)

[Сервер приложений](https://en.wikipedia.org/wiki/Application_server) - сервер, который включает в себя контейнер сервлетов и реализует весь стек [Java EE](https://ru.wikipedia.org/wiki/Jakarta_EE) (ныне Jakarta EE).

Примеры:
- [Wildfly](https://wildfly.org/about/)
- [Apache TomEE](https://tomee.apache.org/)
- [IBM WebSphere](https://www.ibm.com/ru-ru/marketplace/java-ee-runtime)
- [Eclipse GlassFish](https://eclipse-ee4j.github.io/glassfish/)
- [Сводная таблица](https://en.wikipedia.org/wiki/List_of_application_servers)

Исчерпывающий ответ на вопрос "Зачем нужны сервера приложений, если есть Tomcat" можно найти в одноимённой [статье](https://itsobes.ru/JavaSobes/chem-otlichaetsia-servlet-container-ot-application-server/).

[к содержанию](#сопровождение-поддержка-эксплуатация)