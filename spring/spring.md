[Оглавление](../README.md)

# Spring

- [Какие есть `scope` в Spring? Какой по умолчанию? Чем `singleton` отличается от `prototype`? Можно ли сделать свой `scope` и как? Плавно переходит в вопрос "Как заинжектить `prototype` в `singleton`?"](#какие-есть-scope-в-spring-какой-по-умолчанию-чем-singleton-отличается-от-prototype-можно-ли-сделать-свой-scope-и-как-плавно-переходит-в-вопрос-как-заинжектить-prototype-в-singleton)
- [Часто спрашивают о циклических зависимостях бинов в Spring. Проблема ли это или что получим в результате? Если проблема, то как её решить?](#часто-спрашивают-о-циклических-зависимостях-бинов-в-spring-проблема-ли-это-или-что-получим-в-результате-если-проблема-то-как-её-решить)
- [Бывают вопросы про жизненный цикл бина, этапы инициализации контекста, про устройство спринга внутри, про DI и как он работает](#бывают-вопросы-про-жизненный-цикл-бина-этапы-инициализации-контекста-про-устройство-спринга-внутри-про-di-и-как-он-работает)
- [Расскажите про прокси и про `@Transactional`. Как работает и зачем? Какие могут быть проблемы? Можно ли навесить `@Transactional` на приватный метод? А если вызывать метод с `@Transactional` внутри другого метода с `@Transactional` одного класса - будет работать?](#расскажите-про-прокси-и-про-transactional-как-работает-и-зачем-какие-могут-быть-проблемы-можно-ли-навесить-transactional-на-приватный-метод-а-если-вызывать-метод-с-transactional-внутри-другого-метода-с-transactional-одного-класса---будет-работать)
- [Где у обычного (НЕ Boot) Spring-приложения main-класс?](#где-у-обычного-не-boot-spring-приложения-main-класс)
- [Как работает Spring Boot и его стартеры?](#как-работает-spring-boot-и-его-стартеры)
- [Как выполняется http-запрос в Spring?](#как-выполняется-http-запрос-в-spring)

## Какие есть `scope` в Spring? Какой по умолчанию? Чем `singleton` отличается от `prototype`? Можно ли сделать свой `scope` и как? Плавно переходит в вопрос "Как заинжектить `prototype` в `singleton`?"

Spring scope:
- `singleton` (по умолчанию)
- `prototype`
- `request`
- `session`
- `application`
- `websocket`

Про `scope` подробнее можно прочитать в [документации](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-factory-scopes), [Bealdung](https://www.baeldung.com/spring-bean-scopes). И, конечно же, надо посмотреть [Spring-потрошитель Ч. 2](https://www.youtube.com/watch?v=cou_qomYLNU&t=2s).

Про `prototype` в `singleton` можно вспомнить несколько вариантов:
- `@Lookup`
- Фабрика для создания экземпляров `prototype`-бинов
- `ProxyMod = ScopedProxyMode.TARGET_CLASS`

Подробнее о каждом варианте есть в [Bealdung](https://www.baeldung.com/spring-inject-prototype-bean-into-singleton) и смотрим [Spring-потрошитель Ч. 2](https://www.youtube.com/watch?v=cou_qomYLNU&t=2s).

[к содержанию](#spring)

## Часто спрашивают о циклических зависимостях бинов в Spring. Проблема ли это или что получим в результате? Если проблема, то как её решить?

Да, это проблема - будет выброшено исключение `BeanCurrentlyInCreationException` (при внедрении зависимостей через конструктор).

Варианты решения:
- Ещё раз подумать, той ли дорогой мы держим путь - может не поздно сделать редизайн и избавиться от циклических зависимостей
- Инициализировать один из бинов лениво с помощью `@Lazy`
- Внедрение зависимостей в setter-метод, а не в конструктор

Подробнее есть в [документации](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-dependency-resolution) и в [Bealdung](https://www.baeldung.com/circular-dependencies-in-spring)

[к содержанию](#spring)

## Бывают вопросы про жизненный цикл бина, этапы инициализации контекста, про устройство спринга внутри, про DI и как он работает

Тут однозначно надо смотреть Spring-потрошитель [часть 1](https://www.youtube.com/watch?v=BmBr5diz8WA) и [часть 2](https://www.youtube.com/watch?v=cou_qomYLNU&t=2s). Также благое дело - это почитать [документацию](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#spring-core).

Также по этапам инициализации контекста есть [статья](https://habr.com/ru/post/222579/) с красивыми картинками на хабре.

[к содержанию](#spring)

## Расскажите про прокси и про `@Transactional`. Как работает и зачем? Какие могут быть проблемы? Можно ли навесить `@Transactional` на приватный метод? А если вызывать метод с `@Transactional` внутри другого метода с `@Transactional` одного класса - будет работать?

Для начала, если вы не знали или случайно забыли про паттерн Proxy в общем виде, то можно освежиться [здесь](https://refactoring.guru/ru/design-patterns/proxy).

Допустим, что наш сервис `MyServiceImpl` имеет 2 публичных метода, аннотированных `@Transactional` - `method1` и `method2`(он с `Propagation.REQUIRES_NEW`). В `method1` вызываем `method2`. "В связи с тем, что для поддержки транзакций через аннотации используется Spring AOP, в момент вызова `method1()` на самом деле вызывается метод прокси объекта. Создается новая транзакция и далее происходит вызов `method1()` класса `MyServiceImpl`. А когда из `method1()` вызовем `method2()`, обращения к прокси нет, вызывается уже сразу метод нашего класса и, соответственно, никаких новых транзакций создаваться не будет" - это цитата и краткий ответ на вопрос приведена из [статьи](https://habr.com/ru/post/347752/) на хабре, где можно ознакомиться с подробностями.

Что тут можно ещё посоветовать? Spring-потрошитель опять и снова - [часть 1](https://www.youtube.com/watch?v=BmBr5diz8WA) и [часть 2](https://www.youtube.com/watch?v=cou_qomYLNU&t=2s). А также документация является несомненным и любимым первоисточником информации о [Proxy](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop-proxying) и [управление транзакциями](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html).

[к содержанию](#spring)

## Где у обычного (НЕ Boot) Spring-приложения main-класс?

Старое доброе обычное Spring-приложение деплоится в [контейнер сервлетов](https://en.wikipedia.org/wiki/Web_container) (или [сервер приложений](https://en.wikipedia.org/wiki/List_of_application_servers#Java)), где и расположен main-класс. При этом оно собирается в [war-архив](https://en.wikipedia.org/wiki/WAR_(file_format)). Когда war-файл разворачивается в контейнере, контейнер обычно распаковывает его для доступа к файлам, а затем запускает приложение. Spring Boot приложение также можно собрать как war и задеплоить его таким же образом.

Подробнее:
- [Понимание WAR](http://spring-projects.ru/understanding/war/)
- [В чём разница между jar и war?](https://itsobes.ru/JavaSobes/v-chiom-raznitsa-mezhdu-jar-i-war/)
- Конвертация Spring Boot JAR приложения в WAR на [RUS](https://spring-projects.ru/guides/convert-jar-to-war-maven/) или [ENG](https://spring.io/guides/gs/convert-jar-to-war/)

[к содержанию](#spring)

## Как работает Spring Boot и его стартеры?

Во-первых, благодаря [spring-boot-starter-parent](https://www.baeldung.com/spring-boot-starter-parent), у которого родителем является [spring-boot-dependencies](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/maven-plugin/reference/html/#using-import), можно особо не париться о зависимостях и их версиях - [большинство](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-dependency-versions.html) версии того, что может потребоваться прописано и согласовано в [dependencyManagement](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#dependency-management) родительского [`pom`](https://maven.apache.org/pom.html#Inheritance). Или можно заимпортировать [BOM](https://www.baeldung.com/spring-maven-bom).

Spring Boot черпает свою мощь из [стартеров](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter) - наборов сконфигурированных бинов со всеми необходимыми зависимостями, готовых к использованию и доступных для тонкой настройки через properties-файлы.

Для Spring Boot приложения создаётся main-класс с аннотацией [@SpringBootApplication](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-using-springbootapplication-annotation.html) и запуском метода [run](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplication.html#run-java.lang.Class:A-java.lang.String:A-) класса [SpringApplication](https://docs.spring.io/spring-boot/docs/2.1.10.RELEASE/reference/html/boot-features-spring-application.html), который возвращает [ApplicationContext](http://spring-projects.ru/understanding/application-context/).

   ```java
@SpringBootApplication
public class MyApplication {

	public static void main(String[] args) {
		SpringApplication.run(MyApplication.class, args);
	}
}
```
   
Аннотация `@SpringBootApplication` просто скрывает за собой аннотации [@EnableAutoConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/EnableAutoConfiguration.html), [@ComponentScan](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html) и [@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html).

`SpringBootApplication` создаёт либо [WebApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/WebApplicationContext.html) (если в classpath есть [Servlet](https://docs.oracle.com/javaee/7/api/javax/servlet/Servlet.html) и [ConfigurableWebApplicationContext](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/ConfigurableWebApplicationContext.html)), либо [GenericApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/GenericApplicationContext.html).

При создании стартера используется файл [META-INF/spring.factories](https://docs.spring.io/autorepo/docs/spring-boot/2.0.0.M3/reference/html/boot-features-developing-auto-configuration.html) - в нём ключу `org.springframework.boot.autoconfigure.EnableAutoConfiguration` приравнивается список из полных имён всех классов-конфигураций (а в них бины, `@ComponentScan`, `@Import` и т.п.) стартера через запятую.

Аннотация `@EnableAutoConfiguration` импортирует [AutoConfigurationImportSelector](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/AutoConfigurationImportSelector.html), который и отвечает за поиск необходимых классов конфигурации. Вызов метода `getCandidateConfigurations` обращается к [SpringFactoriesLoader](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/support/SpringFactoriesLoader.html) и его методу `loadFactoryNames`, чтобы просканировать classpath на наличие файлов `META-INF/spring.factories` и имен классов-конфигураций в них, а затем загрузить их в контекст.

Также у Spring boot есть модуль `spring-boot-autoconfigure` со своим файлом [META-INF/spring.factories](https://github.com/spring-projects/spring-boot/blob/v2.0.0.M3/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories). Чтобы не создавать все-все бины из конфигураций, у бинов используется аннотация [@Conditional](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Conditional.html) (или её вариации) с каким-либо условием.

Чтобы упаковать Spring boot в jar используется [spring-boot-maven-plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/html/). У такого jar в `META-INF/MANIFEST.MF` будет прописан `Main-Class` - [org.springframework.boot.loader.JarLauncher](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/loader/JarLauncher.html), а в `Start-Class` будет уже main-класс нашего приложения. `JarLauncher` формирует class path (в начале в нём только `org.springframework.boot`), который находится в `BOOT-INF`(там `lib` с зависимостями и `class` с классами приложения), а затем запускает `Start-Class`.

Посмотреть:
- Доклад Евгения Борисова и Кирилла Толкачёва Boot yourself, Spring is coming: [часть 1](https://www.youtube.com/watch?v=yy43NOreJG4&t=1s), [часть 2](https://www.youtube.com/watch?v=7Cq5zEm2wq0). На Хабре есть текстовая расшифровка: [часть 1](https://habr.com/ru/company/jugru/blog/424503/), [часть 2](https://habr.com/ru/company/jugru/blog/425333/).
- Доклад Кирилла Толкачёва и Максима Гореликова [Spring Boot Starter — как и зачем?](https://www.youtube.com/watch?v=2_iE7jZWl3U)
- Доклад Кирилла Толкачёва и Александра Тарасова — [Твой личный Spring Boot Starter](https://www.youtube.com/watch?v=5WdzD15xE-4)

Почитать:
- На Хабре: [Как работает Spring Boot Auto-Configuration](https://habr.com/ru/post/487980/), [Пишем свой spring-boot-starter](https://habr.com/ru/post/275337/) и [Использование Conditional в Spring](https://habr.com/ru/post/462541/)
- Hа Baeldung: [A Comparison Between Spring and Spring Boot](https://www.baeldung.com/spring-vs-spring-boot), [Create a Custom Auto-Configuration with Spring Boot](https://www.baeldung.com/spring-boot-custom-auto-configuration), [Intro to Spring Boot Starters](https://www.baeldung.com/spring-boot-starters), [Spring Boot: Configuring a Main Class](https://www.baeldung.com/spring-boot-main-class)
- [What is Spring Boot? Autoconfigurations In-Depth](https://www.marcobehler.com/guides/spring-boot)
- [Spring Boot for beginners](https://github.com/in28minutes/SpringBootForBeginners)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-documentation)
- [Список](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters) готовых стартеров

[к содержанию](#spring)

## Как выполняется http-запрос в Spring?

В современном Spring есть два подхода к построению веб-приложений:
- Spring MVC
- Spring WebFlux

Работа Spring MVC строится вокруг [DispatcherServlet](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/DispatcherServlet.html), который является обычным `Servlet`'ом и реализует паттерн [Front Controller](http://design-pattern.ru/patterns/front-controller.html): принимает Http-запросы и координирует их с требуемыми обработчиками. Для своей конфигурации `DispatcherServlet` [использует](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-servlet-context-hierarchy) [WebApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/WebApplicationContext.html). `DispatcherServlet` в обработке запроса помогают несколько ["специальных бинов"](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-servlet-special-bean-types) [следующим образом](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-servlet-sequence):
1. После получения HTTP-запроса `DispatcherServlet` перебирает доступные ему (предварительно найденные в контексте) экземпляры [HandlerMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerMapping.html), один из которых определит, метод какого `Controller` должен быть вызван. Реализации `HandlerMapping`, использующиеся по умолчанию: [BeanNameUrlHandlerMapping](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/handler/BeanNameUrlHandlerMapping.html) и [RequestMappingHandlerMapping](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/RequestMappingHandlerMapping.html) (создаёт экземпляры [RequestMappingInfo](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/RequestMappingInfo.html) по методам аннотированным [@RequestMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html) в классах с аннотацией [@Controller](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html)). `HandlerMapping` по [HttpServletRequest](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html?is-external=true) находит соответствующий обработчик - handler-объект (например, [HandlerMethod](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/HandlerMethod.html)). Каждый `HandlerMapping` может иметь несколько реализаций [HandlerInterceptor](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html) - интерфейса для кастомизации [пред- и постобработки](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-handlermapping-interceptor) запроса. Список из `HandlerInterceptor`'ов и handler-объекта образуют экземпляр класса [HandlerExecutionChain](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerExecutionChain.html), который возвращается в `DispatcherServlet`.
2. Для выбранного обработчика определяется соответствующий [HandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/HandlerAdapter.html) из предварительно найденных в контексте. По умолчанию используются [HttpRequestHandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/HttpRequestHandlerAdapter.html) (поддерживает классы, реализующие интерфейс [HttpRequestHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/HttpRequestHandler.html)), [SimpleControllerHandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/SimpleControllerHandlerAdapter.html) (поддерживает классы, реализующие интерфейс [Controller](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/Controller.html)) или [RequestMappingHandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/RequestMappingHandlerAdapter.html) (поддерживает контроллеры с аннотацией `@RequestMapping`).
3. Происходит вызов метода `applyPreHandle` объекта `HandlerExecutionChain`. Если он вернёт `true`, то значит все `HandlerInterceptor` выполнили свою предобработку и можно перейти к вызову основного обработчика. `false` будет означать, что один из `HandlerInterceptor` взял обработку ответа на себя в обход основного обработчика. 
4. Выбранный `HandlerAdapter` извлекается из `HandlerExecutionChain` и с помощью метода `handle` принимает объекты запроса и ответа, а также найденный метод-обработчик запроса.
5. Метод-обработчик запроса из `Controller` (вызванный через `handle`) выполняется и возвращает в `DispatcherServlet` [ModelAndView](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/ModelAndView.html). При помощи интерфейса [ViewResolver](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/ViewResolver.html) `DispatcherServlet` [определяет](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-viewresolver), какой [View](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/View.html) нужно использовать на основании полученного имени. 
   Если мы имеем дело с REST-Controller или RESTful-методом контроллера, то вместо `ModelAndView` в `DispatcherServlet` из `Controller` вернётся `null` и, соответственно, никакой `ViewResolver` задействован не будет - ответ сразу будет полностью содержаться в теле [HttpServletResponse](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletResponse.html) после выполнения `handle`. Чтобы определить RESTful-методы, достаточно аннотировать их [@ResponseBody](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/ResponseBody.html) либо вместо `@Controller` у класса поставить [@RestController](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html), если все методы котроллера будут RESTful.
6. Перед завершением обработки запроса у объекта `HandlerExecutionChain` вызывается метод `applyPostHandle` для постобработки с помощью `HandlerInterceptor`ов.
7. Если в процессе обработки запроса выбрасывается исключение, то оно [обрабатывается](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-exceptionhandlers) с помощью одной из реализаций интерфейса [HandlerExceptionResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerExceptionResolver.html). По умолчанию используются [ExceptionHandlerExceptionResolver](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/ExceptionHandlerExceptionResolver.html) (обрабатывает исключени из методов, аннотированных [@ExceptionHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ExceptionHandler.html)), [ResponseStatusExceptionResolver](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/annotation/ResponseStatusExceptionResolver.html) (используется для отображения исключений аннотированных [@ResponseStatus](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/ResponseStatus.html) в коды HTTP-статусов) и [DefaultHandlerExceptionResolver](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/support/DefaultHandlerExceptionResolver.html) (отображает стандартные исключения Spring MVC в коды HTTP-статусов).
8. В случае с классическим `Controller` после того, как `View` создан, `DispatcherServlet` отправляет данные в виде атрибутов в `View`, который в конечном итоге записывается в `HttpServletResponse`. Для REST-Controller ответ данная логика не вызывается, ведь ответ уже в `HttpServletResponse`.

Когда HTTP запрос приходит с указанным заголовком Accept, Spring MVC перебирает доступные [HttpMessageConverter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/converter/HttpMessageConverter.html) до тех пор, пока не найдет того, кто сможет конвертировать из типов POJO доменной модели в указанный тип заголовка Accept. `HttpMessageConverter` работает в обоих направлениях: тела входящих запросов конвертируются в Java объекты, а Java объекты конвертируются в тела HTTP ответов.

По умолчанию, Spring Boot определяет довольно обширный набор реализаций `HttpMessageConverter`, подходящие для использования широкого круга задач, но также можно добавить поддержку и для других форматов в виде собственной или сторонней реализации `HttpMessageConverter` или переопределить существующие.

Также стоит упомянуть, что как и в случае любого другого сервлета, к обработке запроса в Spring MVC может быть применена одна из реализаций интерфейса [javax.servlet.Filter](https://docs.oracle.com/javaee/7/api/javax/servlet/Filter.html?is-external=true) как до выполнения запроса, так и после. Sring MVC предоставляет несколько уже [готовых реализаций](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/filter/package-summary.html).

Отдельного разговора заслуживает путь запроса по внутренностям Spring Security, где используется множество различных фильтров. На хабре есть [статья](https://habr.com/ru/post/346628/) об этом.

Подробнее:
- [Spring MVC — основные принципы](https://habr.com/ru/post/336816/) на Хабре
- [Путь запроса по внутренностям Spring Security](https://habr.com/ru/post/346628/) на Хабре
- [An Intro to the Spring DispatcherServlet](https://www.baeldung.com/spring-dispatcherservlet) на Bealdung
- [HandlerAdapters in Spring MVC](https://www.baeldung.com/spring-mvc-handler-adapters) на Bealdung
- [Quick Guide to Spring Controllers](https://www.baeldung.com/spring-controllers) на Bealdung
- [Spring RequestMapping](https://www.baeldung.com/spring-requestmapping) на Bealdung
- [Http Message Converters with the Spring Framework](https://www.baeldung.com/spring-httpmessageconverter-rest) на Bealdung
- [How to Define a Spring Boot Filter?](https://www.baeldung.com/spring-boot-add-filter) на Bealdung
- [Spring Professional Study Notes](https://jakubstas.com/spring-professional-study-notes/#.WUaMx2jytzo)
- [Spring Security Architecture](https://spring.io/guides/topicals/spring-security-architecture/)
- [Схематично](https://terasolunaorg.github.io/guideline/1.0.1.RELEASE/en/Overview/SpringMVCOverview.html#id1)

Документация:
- [Spring Web MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc)
- [Spring MVC Auto-configuration в Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-spring-mvc-auto-configuration)

Spring WebFlux - это реактивный веб-фреймворк, который появился в Spring Framework 5.0. Он не требует Servlet API (но может использовать Servlet 3.1+containers, хотя чаще это [Netty](https://netty.io/) (по умолчанию в Spring Boot) или [Undertow](https://undertow.io/)), полностью асинхронный и неблокирующий, реализует спецификацию [Reactive Streams](https://www.reactive-streams.org/) при помощи [проекта Reactor](https://projectreactor.io/).

В Spring WebFlux используется большинство аннотаций из Spring MVC (RestController, RequestMapping и другие) для определения [аннотированных контроллеров](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-controller). Однако представляет новую возможность создания [функциональных котроллеров](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-fn), основанных на [HandlerFunction](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-fn-handler-functions).

В Spring WebFlux обработка запроса на стороне сервера строится в два уровня:
- [HttpHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/server/reactive/HttpHandler.html) - это базовый интерфейс обработки HTTP-запросов с использованием неблокирующего I/O, Reactive Streams back pressure через адаптеры для Reactor Netty, Undertow и т.д.
- [WebHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/WebHandler.html) - интерфейс, который предоставляет верхнеуровневое API для обработки HTTP-запросов поверх аннотированных или функциональных контроллеров.

Контракт `HttpHandler` представляет обработку HTTP-запроса, как его прохождение через цепочку множества [WebExceptionHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/WebExceptionHandler.html), множества [WebFilter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/WebFilter.html) и одного единственного [WebHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/WebHandler.html). Сборкой цепочки занимается [WebHttpHandlerBuilder](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/adapter/WebHttpHandlerBuilder.html) при помощи [ApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html).

Диспетчеризация запросов в Spring WebFlux выполняется [DispatcherHandler](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/DispatcherHandler.html), который является имплементацией интерфейса [WebHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/server/WebHandler.html) и также реализует паттерн [Front Controller](http://design-pattern.ru/patterns/front-controller.html): принимает Http-запросы и координирует их с требуемыми обработчиками. `DispatcherHandler` - это Spring bean, имплементирующий [ApplicationContextAware](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContextAware.html) для доступа к контексту, с которым он был запущен. `DispatcherHandler` с бин-именем `webHandler` обнаруживает `WebHttpHandlerBuilder` и помещает в цепочку в качестве `WebHandler`.

`DispatcherHandler` в ходе обработки http-запроса и ответа делегирует часть работы ["специальным бинам"](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-special-bean-types), которые могут быть подвержены кастомизации, расширению и замене пользователем. Сам процесс обработки выглядит следующим образом:

```java
    @Override
	public Mono<Void> handle(ServerWebExchange exchange) {
		if (this.handlerMappings == null) {
			return createNotFoundError();
		}
		return Flux.fromIterable(this.handlerMappings)
				.concatMap(mapping -> mapping.getHandler(exchange))
				.next()
				.switchIfEmpty(createNotFoundError())
				.flatMap(handler -> invokeHandler(exchange, handler))
				.flatMap(result -> handleResult(exchange, result));
	}
```

1. Каждый из экземпляров [HandlerMapping](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/HandlerMapping.html) пытается найти подобающий обработчик для данного запроса (какой-то метод, какого-то контроллера). В итоге выбирается первый найденный обработчик (`handler`). Основными доступными реализациями `HandlerMapping` являются:
    - [RequestMappingHandlerMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/result/method/annotation/RequestMappingHandlerMapping.html) для методов-обработчиков, аннотированных `@RequestMapping`
    - [RouterFunctionMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/function/server/support/RouterFunctionMapping.html) для функциональных обработчиков
    - [SimpleUrlHandlerMapping](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/handler/SimpleUrlHandlerMapping.html) для маппинга URL-ов на бины-обработчики запросов
2. Если обработчик найден, то для него (в `invokeHandler`) выбирается подходящий [HandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/HandlerAdapter.html), вызывается его метод `handle` для непосредственной обработки запроса выбранным обработчиком. Результат обработки упаковывается в [HandlerResult](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/HandlerResult.html), который возвращается в `DispatcherHandler`. Главная задача `HandlerAdapter` - скрыть детали и способ непосредственного вызова метода-обработчика от `DispatcherHandler`. Примерами доступных реализаций `HandlerAdapter` являются:
    - [RequestMappingHandlerAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/result/method/annotation/RequestMappingHandlerAdapter.html) - для вызова методов, аннотированных `@RequestMapping`
    - [HandlerFunctionAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/function/server/support/HandlerFunctionAdapter.html) - для вызова [HandlerFunctions](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/function/server/HandlerFunction.html)
3. Полученный `HandlerResult` обрабатывается (в `handleResult`) необходимым для него [HandlerResultHandler](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/HandlerResultHandler.html). Здесь обработка завершается формированием ответа на запрос требуемым образом. По умолчанию [доступно](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-resulthandling) несколько реализаций `HandlerResultHandler`:
    - [ResponseEntityResultHandler](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/result/method/annotation/ResponseEntityResultHandler.html) - обрабатывает [ResponseEntity](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/ResponseEntity.html), обычно из `@Controller`
    - [ServerResponseResultHandler](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/function/server/support/ServerResponseResultHandler.html) - обрабатывает [ServerResponse](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/function/server/ServerResponse.html), обычно из функциональных контроллеров
    - [ResponseBodyResultHandler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/result/method/annotation/ResponseBodyResultHandler.html) - обрабатывает возвращаемые значения из методов, аннотированных `@ResponseBody`, или методов класса `@RestController`
    - [ViewResolutionResultHandler](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/reactive/result/view/ViewResolutionResultHandler.html) - инкапсулирует в себе алгоритм [View Resolution](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-viewresolution) и обработку поддерживаемых данным алгоритмом типов результатов

Документация:
- [Spring WebFlux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux)
- [Spring WebFlux Auto-configuration в Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-webflux-auto-configuration)

[к содержанию](#spring)