[Оглавление](../README.md)

# SQL

- [В чем разница между where и having в SQL?](#в-чем-разница-между-where-и-having-в-sql)

## В чем разница между where и having в SQL?

Есть исчерпывающий [ответ](https://www.postgresql.org/docs/12/tutorial-agg.html) на этот вопрос в документации к Postgresql. Вот его перевод с сайта [Postgres Pro](https://postgrespro.ru/docs/postgresql/12/tutorial-agg):
>Основное отличие WHERE от HAVING заключается в том, что WHERE сначала выбирает строки, а затем группирует их и вычисляет агрегатные функции (таким образом, она отбирает строки для вычисления агрегатов), тогда как HAVING отбирает строки групп после группировки и вычисления агрегатных функций. Как следствие, предложение WHERE не должно содержать агрегатных функций; не имеет смысла использовать агрегатные функции для определения строк для вычисления агрегатных функций. Предложение HAVING, напротив, всегда содержит агрегатные функции. (Строго говоря, вы можете написать предложение HAVING, не используя агрегаты, но это редко бывает полезно. То же самое условие может работать более эффективно на стадии WHERE.)

Но вас также может заинтересовать:
- [Как работает реляционная БД](https://habr.com/ru/company/mailru/blog/266811/) на Хабре
- [How does a relational database work](https://vladmihalcea.com/how-does-a-relational-database-work/)
- [How does a relational database execute SQL statements and prepared statements](https://vladmihalcea.com/relational-database-sql-prepared-statements/)
- [How Database Internally Works](https://dev.to/dhanushkadev/how-database-internally-works-pfh)
- [Курс](https://www.youtube.com/playlist?list=PL-_cKNuVAYAWMRG7iKHGZMg9Az8HX735w) по БД от Ильи Тетерина или [курс](https://www.youtube.com/playlist?list=PL-_cKNuVAYAU-CO9NXtjzqdUsXoK047DZ) от Вадима Цесько
- [Let's Build a Simple Database](https://cstack.github.io/db_tutorial/)
- [Database internals](https://www.databass.dev/)

[к содержанию](#sql)