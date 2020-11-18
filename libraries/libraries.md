[Оглавление](../README.md)

# Популярные библиотеки

- [Стоит ли использовать Lombok?](#стоит-ли-использовать-lombok)

## Стоит ли использовать Lombok?

Осторожно, холиворный вопрос! Возможны ожоги и прочие неприятности!

На Хабре [найдётся много больше одной статьи](https://habr.com/ru/search/?q=Lombok#h) демонстрирующей народную любовь к Lombok - ведь он знаменитый победитель бойлерплейтов, сократитель кода, ускорятель кодонабора, любимый сундучок исцеляющих костылей и прочее, и прочее. О прелести Lombok вам расскажут из любого утюга.

Но как там с подводными камнями? Что с надводными вилами? Этот вопрос на собеседовании о том, как хорошо вы знаете недостатки своего любимого инструмента. И на сдачу - тест на совместимость.

А ответ на вопрос стоит начать с переворота стрелочки - с хитрым ленинским прищуром спросите у интервьюера: "А как в вашем стайл-гайде написано?". Заодно узнаете о его ~~отсутствии~~ наличии. Ибо холиварные вопросы решить нужно однажды на сходе Архитектурного Синода и на весь простор компании сии решения распространить, увековечив их в стайл-гайде, дабы раскола избежать и не соблазнять неокрепшие умы неофитов спорами на рабочем месте посреди спринта горящего.

Самая лайтовая и, возможно, притянутая за уши причина не использовать Lombok - плагин постоянно отваливается с выходом новой версии IDEA. А если отваливается плагин, то работа летит коту под хвост. Но это уже уходит в прошлое! С версии 2020.3 Lombok-плагин [будет поставляться вместе с IDEA](https://blog.jetbrains.com/idea/2020/10/intellij-idea-2020-3-eap5/).

Большинство из проблем с Lombok связаны с особенностями и нюансами реализации как самих аннотаций библиотеки, так и сторонних фреймворков и библиотек. Например, эксперты по Hibernate советуют (например [тут](https://thorben-janssen.com/lombok-hibernate-how-to-avoid-common-pitfalls/)) избегать использования Lombok в entity-классах. Хотя бы исключить использование [@EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode), [@ToString](https://projectlombok.org/features/ToString) и [@Data](https://projectlombok.org/features/Data), потому что генерируемый ими код для методов `equals`, `hashCode` и `toString` может либо отработать неверно, либо привести к потере производительности или исключениям в некоторых определённых случаях для entities. А как нужно правильно реализовывать данные методы для entities написано [тут](https://vladmihalcea.com/the-best-way-to-implement-equals-hashcode-and-tostring-with-jpa-and-hibernate/) или [тут](https://thorben-janssen.com/ultimate-guide-to-implementing-equals-and-hashcode-with-hibernate/).

Интересная дискуссия, затянувшаяся на два дня, была по данному вопросу в [чатике](https://t.me/pspodcast_group) подкаста "Паша+Слава" 7-8 марта - начало [тут](https://t.me/pspodcast_group/10280). В ней @asm0dey задаёт каверзные вопросы по использованию Lombok и рассказывает почему он забанил Lombok как тимлид. Например: "Что произойдёт если библиотека, которую ты используешь, использует [@SneakyThrows](https://projectlombok.org/features/SneakyThrows) и ты её вызываешь в рамках транзакции?" (об этом можно почитать [тут](https://blog.pchudzik.com/201911/transactional-errors/)), "Тебе наверное нравится аннотация [@Data](https://projectlombok.org/features/Data). Как она строит equals для коллекций и для массивов?", "Знаешь ли ты что [@EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode) может вызвать StackOverflowException и тебе даже нечего будет дебажить?".

Главная проблема Lombok - простота хуже воровства - его волшебная лёгкость провоцирует не читать документацию, что потом может повлечь за собой горе и страдания. Но может и не повлечь - тут как задача в джиру ляжет. Недаром не заросла к Lombok народная тропа - можно ограничиться использованием некоторого минимального джентельменского набора из Lombok, следить за собой. Но всё-таки ~~мойте руки, предохраняйтесь~~ читайте документацию перед активным использованием ваших инструментов, ведь они могут иметь неочевидные нюансы и сюрпризы в реализации.

P.S. У Lombok есть занятная экспериментальная фича [onX](https://projectlombok.org/features/experimental/onX) - у неё интересный синтаксис (`@__` правда только для Java 7) и его описание в документации. А вот [тут](https://www.baeldung.com/spring-injection-lombok) рассказывается зачем оно вам может понадобиться, например в Spring-приложении.

Подробнее:
- [Project Lombok](https://projectlombok.org/)
- [Who Needs Lombok Anyhow](http://gregorriegler.com/2019/08/10/who-needs-lombok-anyhow.html)
- [Be Careful With Lombok](https://levelup.gitconnected.com/be-careful-with-lombok-2e2edfc01110)
- [Don’t use Lombok](https://medium.com/@vgonzalo/dont-use-lombok-672418daa819)
- [Lombok & Hibernate: How to Avoid Common Pitfalls](https://thorben-janssen.com/lombok-hibernate-how-to-avoid-common-pitfalls/)
- [Error handling with @Transactional and @SneakyThrows](https://blog.pchudzik.com/201911/transactional-errors/)
- [Lombok, sources.jar и удобный дебаг](https://habr.com/ru/company/sberbank/blog/438548/)
- [Some Dangers of Using Lombok](https://medium.com/@gabor.liptak/some-dangers-of-using-lombok-d759fc8f701f)

[к содержанию](#популярные-библиотеки)