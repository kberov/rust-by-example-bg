# Тестване

Ръждьо е език за програмиране, който много държи на точността, а това включва
поддръжка за писане на софтуерни тестове в самия език.

Тестването се проявява в три разновидности:

* [Поединично][unit][^unit] тестване (тестване на единици програмен код).
* [Документално][doc] тестване (тестове в документацията).
* Тестване при [внедряване][integration][^integration].

Освен това Ръждьо поддържа указване на зависимости по време на
разработка[^dev-dependencies] – за
тестване:

* [Зависимости при разработка][dev-dependencies]

## Б.пр.

[^unit]: поединично тестване – unit testing

[^integration]: внедряване – integration

[^dev-dependencies]: зависимости по време на разработка – dev-dependencies

## See Also

* [„Книгата”][doc-testing] главата за тестване
* [Съвети за добро ППВ][doc-nursery] за тестването на документация

[unit]: testing/unit_testing.md
[doc]: testing/doc_testing.md
[integration]: testing/integration_testing.md
[dev-dependencies]: testing/dev_dependencies.md
[doc-testing]: https://doc.rust-lang.org/book/ch11-00-testing.html
[doc-nursery]: https://rust-lang-nursery.github.io/api-guidelines/documentation.html
