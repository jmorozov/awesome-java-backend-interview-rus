[Оглавление](../README.md)

# Collection Framework

- [Вопросы про методы `Equals`, `hashcode` и их связь с `HashMap`.](#вопросы-про-методы-equals-hashcode-и-их-связь-с-hashmap)
- [Вопросы про списки: какие есть, алгоритмическая сложность, какой брать для вставки в середину, в конец, в огурец.](#вопросы-про-списки-какие-есть-алгоритмическая-сложность-какой-брать-для-вставки-в-середину-в-конец-в-огурец)
- [Перечислите методы класса `Object`.](#перечислите-методы-класса-object)
- [Что можно положить и достать из `List<? extends Number>`, а что с `List<? super Number>`? Что такое ковариантность, контрвариантность, инвариантность?](#что-можно-положить-и-достать-из-list-extends-number-а-что-с-list-super-number-что-такое-ковариантность-контрвариантность-инвариантность)
- [Что внутри и как работают TreeSet/TreeMap? В чем идея Красно-черного дерева?](#что-внутри-и-как-работают-treesettreemap-в-чем-идея-красно-черного-дерева)

## Вопросы про методы `Equals`, `hashcode` и их связь с `HashMap`. 

Контракт. Далее разговор переходит к устройству `HashMap`. Как устроена внутри? А происходит в случае возникновения коллизии? Назовите алгоритмические сложности поиска, чтения, удаления из элемента мапы. А что если ключ - это массив байтов? А может быть так, что мы положим элемент в мапу, а потом не найдем? Обсасывают бедную мапу со всех сторон. Самая популярная тема для обсуждения.

Контракт `equals` и `hashcode`:
   1. Для одного и того же объекта хэшкоды одинаковые.
   1. Если объекты равны по equals, то и хэшкоды одинаковые.
   1. Если же хэшкоды равны, то объекты могут быть не равны по equals (коллизия).
   1. Если хэшкоды разные, то и объекты разные.
   
   В [статье](https://habr.com/ru/post/168195/) на Хабре это подробно разобрано, если кому-то покажется мало.

Про `HashMap` и вопросы по ним есть несколько отличных статей на Хабре ([в картинках](https://habr.com/ru/post/128017/), [с дополнениями из Java 8](https://habr.com/ru/post/421179/), [а тут вопросы-ответы про коллекциям](https://habr.com/ru/post/162017/)). Кроме того, можно посмотреть исходный код в вашей любимой IDE. Можете сделать себе конспект и повесить на стену :)

[к содержанию](#collection-framework)

## Вопросы про списки: какие есть, алгоритмическая сложность, какой брать для вставки в середину, в конец, в огурец.

По сути это вопрос про `ArrayList` vs `LinkedList`. Опять же, заезженная пластинка, разобранная на Хабре - [вопросы-ответы про коллекциям](https://habr.com/ru/post/162017/), [ArrayList в картинках](https://habr.com/ru/post/128269/), [LinkedList в картинках](https://habr.com/ru/post/127864/), [Что «под капотом» у LinkedList](https://habr.com/ru/post/337558/). Посмотреть исходники тоже полезно. Например, можно понтануться тем, что вставка в середину в `ArrayList` выполняется с помощью нативно реализованной функции `System.arraycopy`, поэтому не всё так плохо, как могло бы быть в этом случае.

[к содержанию](#collection-framework)

## Перечислите методы класса `Object`. 

Этот вопрос далее перетекает либо в обсуждение `HashMap`, либо в основы многопоточного программирования на Java.

Чтобы вы вдруг внезапно не забыли каких-то методов (как это сделал я :D), привожу вам список и [ссылку на JavaDoc](https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/lang/Object.html):
- clone
- equals
- finalize (Deprecated)
- getClass
- hashCode
- toString
- notify
- notifyAll
- wait

Также можно почитать, что там вообще есть в исходниках `Object` в [статье](https://habr.com/ru/post/265373/) на Хабре.

[к содержанию](#collection-framework)

## Что можно положить и достать из `List<? extends Number>`, а что с `List<? super Number>`? Что такое ковариантность, контрвариантность, инвариантность?

Тут речь пойдёт про PECS - Producer extends, Consumer super (Joshua Bloch, [Effective Java](https://books.google.ru/books?id=ka2VUBqHiWkC&pg=PA136&lpg=PA136&dq=bloch+effective+java+pecs+mnemonic&source=bl&ots=yYKnPjt-P-&sig=JGT8qexAAldJ5xYPepbBQ5uude0&hl=en&sa=X&ei=Cb3GUv-sNIuK5ATM7oD4Dw&ved=0CCgQ6AEwAA#v=onepage&q=bloch%20effective%20java%20pecs%20mnemonic&f=false)). А также вариантность — перенос наследования исходных типов на производные от них типы (контейнеры, делегаты, обобщения).

Ковариантность (covariance) - перенос наследования исходных типов на производные от них типы в прямом порядке.
Переменной типа __List<? *extends* T>__ разрешено присвоить экземпляр списка, параметризованного _T_ или его подклассом, но не родительским классом. В список типа __List<? *extends* T>__ нельзя добавить никакой объект (можно только `null`) - нельзя гарантировать какого именно типа экземпляр списка будет присвоен переменной, поэтому нельзя гарантировать, что добавляемый объект разрешён в таком списке. Однако, из списка можно прочитать объект и он будет типа _T_ и экземпляром либо _T_, либо одного из подклассов _T_.
Соответственно, `List<? extends Number>` можно присвоить `ArrayList<Number>` или `ArrayList<Integer>`, но не `ArrayList<Object>`. Метод `get` возвращает `Number`, за которым может скрываться экземпляр `Integer` или другого наследника `Number`.
Массивы также ковариантны.
Переопределение методов, начиная с Java 5, ковариантно относительно типа результата и исключений.

`List<?>` аналогичен `List<? extends Object>` со всеми вытекающими.

Контрвариантность (contravariance) - перенос наследования исходных типов на производные от них типы в обратном порядке.
Переменной типа __List<? *super* T>__ разрешено присвоить экземпляр списка, параметризованного _T_ или его родительским классом, но не его подклассом. В список типа __List<? *super* T>__ можно добавить экземпляр _T_ или его подкласса, но нельзя добавить экземпляр родительских для _T_ классов. Из такого списка с гарантией можно прочитать только `Object`, за которым может скрываться неизвестно какой его подкласс.

Соответственно, `List<? super Number>` можно присвоить либо `ArrayList<Number>`, либо `ArrayList<Object>`, но не список наследников `Number`(т.е. никаких `ArrayList<Integer>`). Можно добавить экземпляр `Integer` или `Double` (можно было бы `Number`, но он абстрактный), но нельзя - `Object`. Метод `get` возвращает `Object` - точнее сказать нельзя.

Инвариантность - наследование исходных типов не переносится на производные.
Переменной типа __List<T>__ разрешено присвоить экземпляр списка, параметризованного только _T_. В список можно добавить экземпляр _T_ или его подкласса. Список возвращает _T_, за которым может скрываться экземпляр его подкласса.

Соответственно, `List<Number>` можно присвоить `ArrayList<Number>`, но не `ArrayList<Integer>` или `ArrayList<Object>`. Можно добавить экземпляр `Integer` или `Double` (можно было бы `Number`, но он абстрактный), но нельзя - `Object`. Метод `get` возвращает `Number`, за которым может скрываться экземпляр `Integer` или другого наследника `Number`.

Подробнее:
- На Хабре: [Погружаемся в Generics](https://habr.com/ru/company/sberbank/blog/416413/), [Используем в API](https://habr.com/ru/post/207360/), изучаем [вариантность в программировании](https://habr.com/ru/post/218753/)
- Посмотреть доклад Александра Маторина [Неочевидные Дженерики](https://www.youtube.com/watch?v=_0c9Fd9FacU&feature=youtu.be&t=1204)
- В одном из ответов на вопрос [Generics FAQ](http://www.angelikalanger.com/GenericsFAQ/FAQSections/TypeArguments.html#FAQ103)
- [Как ограничивается тип generic параметра?](https://itsobes.ru/JavaSobes/kak-ogranichivaetsia-tip-generic-parametra/)
- [Что такое ковариантность и контравариантность?](https://itsobes.ru/JavaSobes/chto-takoe-kovariantnost-i-kontravariantnost/)
- В одном из объяснений на StackOverflow: [раз](https://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java), [два](https://stackoverflow.com/questions/2776975/how-can-i-add-to-list-extends-number-data-structures/2777297#2777297), [три](https://stackoverflow.com/questions/2723397/what-is-pecs-producer-extends-consumer-super)
- [Ковариантность и контравариантность](https://habibutsu.github.io/variance.html) с точки зрения математики, теории категорий и программирования
- [Ковариантность и контравариантность](https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)) в Wikipedia
- [Wildcards](https://docs.oracle.com/javase/tutorial/java/generics/wildcards.html) в официальном туториале Oracle

[к содержанию](#collection-framework)

## Что внутри и как работают TreeSet/TreeMap? В чем идея Красно-черного дерева?

[TreeMap](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/TreeMap.html) - реализация [NavigableMap](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/NavigableMap.html), основанная на [красно-чёрном дереве](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree). Элементы отсортированы по ключам в [натуральном порядке](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Comparable.html) или с помощью [Comparator](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Comparator.html), указанного при создании мапы, в зависимости от использовавшегося конструктора. Гарантирует логарифмическое время выполнения методов `containsKey`, `get`, `put` и `remove`.
[TreeSet](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/TreeSet.html) - реализация [NavigableSet](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/NavigableSet.html), основанная на `TreeMap`. Элементы отсортированы в [натуральном порядке](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Comparable.html) или с помощью [Comparator](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Comparator.html), указанного при создании множества, в зависимости от использовавшегося конструктора. Гарантирует логарифмическое время выполнения методов `add`, `contains` и `remove`.

Обе коллекции НЕ `synchronized` и итератор по ним может выбросить [ConcurrentModificationException](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/ConcurrentModificationException.html).

Если в эти коллекции при использовании натурального порядка сортировки в качестве ключа попытаться положить `null`, то получим `NullPointerException`. В случае с компаратором поведение с `null` будет зависеть от реализации компаратора. До 7-й Java с добавлением `null` в `TreeMap` и `TreeSet` был [баг](https://habr.com/ru/post/164027/).

Самая важная особенность красно-чёрного дерева в том, что оно умеет само себя балансировать, поэтому не важно в каком порядке будут добавляться в него элементы, преимущества этой структуры данных будут сохраняться. Сбалансированность достигается за счёт поддержания правил красно-чёрной раскраски вершин:
- Вершина может быть либо красной, либо чёрной и имеет двух потомков
- Красная вершина не может быть дочерней для красной вершины
- Количество чёрных вершин от корня до листа включительно одинаково для любого листа
- Корень дерева является чёрным
- Все листья — чёрные и не содержат данных

Подробнее:
- [Статья](https://habr.com/ru/post/66926/) про сбалансированные бинарные деревья на Хабре
- [Java собеседование. Коллекции](https://habr.com/ru/post/162017/) на Хабре
- [Java TreeMap vs HashMap](https://www.baeldung.com/java-treemap-vs-hashmap)
- [10 TreeMap Java Interview Questions](https://javahungry.blogspot.com/2014/06/how-treemap-works-ten-treemap-java-interview-questions.html) и [TreeSet Interview Questions](https://javahungry.blogspot.com/2015/10/how-treeset-works-internally-in-java-interview-questions.html)
- [Internal Working of TreeMap in Java](https://www.dineshonjava.com/internal-working-of-treemap-in-java/)
- [A Guide to TreeMap in Java](https://www.baeldung.com/java-treemap) и [A Guide to TreeSet in Java](https://www.baeldung.com/java-tree-set) на Bealdung
- [Красно-черные деревья: коротко и ясно](https://habr.com/ru/post/330644/) на Хабре
- [Балансировка красно-чёрных деревьев — Три случая](https://habr.com/ru/company/otus/blog/472040/) на Хабре
- [Красно-чёрное дерево](https://neerc.ifmo.ru/wiki/index.php?title=%D0%9A%D1%80%D0%B0%D1%81%D0%BD%D0%BE-%D1%87%D0%B5%D1%80%D0%BD%D0%BE%D0%B5_%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE)
- [Визуализация](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html) красно-чёрного дерева. И [ещё](http://www.cs.armstrong.edu/liang/animation/web/RBTree.html). А вот [исходники](https://github.com/nadakamel/RedBlackTree-Visualization)

[к содержанию](#collection-framework)