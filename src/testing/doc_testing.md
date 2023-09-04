# Тестове в документацията

Главният начин да се документира проект в Ръждьо е чрез добавяне на бележки в
изходния код. Документалните коментари се пишат според [Спецификацията на
CommonMark Markdown][commonmark] и поддържат блокове от код в тях. Ръждьо се
грижи за правилността, така че тези блокове от код се компилират и ползват като
тестове от документацията.

```rust,ignore
/// Първият ред е кратко общо описание на функцията.
///
/// Следващите редове представляват подробна документация. Блоковете код
/// започват с тройни обратни кавички и имат в себе си `fn main()` и `extern crate
/// <cratename>`. Да си представим, че тестваме коша `doccomments`:
///
/// ```
/// let result = doccomments::add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

/// Обикновено документалните коментари може да включват раздели „Examples”,
/// „Panics” и „Failures”.
///
/// Следващата функция дели едно число на друго.
///
/// # Examples
///
/// ```
/// let result = doccomments::div(10, 2);
/// assert_eq!(result, 5);
/// ```
///
/// # Panics
///
/// Функцията се паникьосва, ако вторият аргумент е нула.
///
/// ```rust,should_panic
/// // паникьосва се при делене на нула.
/// doccomments::div(10, 0);
/// ```
pub fn div(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("Divide-by-zero error");
    }

    a / b
}
```

Блоковете код в документацията биват тествани автоматично, щом изпълните
командата `cargo test`:

```shell
$ cargo test
running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests doccomments

running 3 tests
test src/lib.rs - add (line 7) ... ok
test src/lib.rs - div (line 21) ... ok
test src/lib.rs - div (line 31) ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

## Основания за тестове в документацията

Главната цел на документалните тестове е да служат като примери, които ползват
предоставяната функционалност, а това е най-важното
[ръководство][question-instead-of-unwrap]. То ни позволява да ползваме примерите
от документацията като пълноценни парчета код. Но ползването на `?` прави така,
че компилацията се проваля, понеже `main` връща `unit`. В този случай можем да
скрием няколко реда код от документацията: Можем да напишем `fn try_main() ->
Result<(), ErrorType>`, да я скрием и разгърнем (`unwrap`) в скритата `main`.
Сложно ли звучи? Ето пример:

```rust,ignore
/// Използване на скрита `try_main` в документални тестове.
///
/// ```
/// # // Скритите редове започват със знака `#` но са също компилируеми!
/// # // Ред, който обгръща тялото, показано в документацията.
/// # fn try_main() -> Result<(), String> {
/// let res = doccomments::try_div(10, 2)?;
/// # Ok(()) // Връщаме от try_main.
/// # }
/// # fn main() { // Започва `main`, която извиква `unwrap()`.
/// #    try_main().unwrap(); // Извикваме try_main и разгръщаме,
/// #                         // така че тестът ще се паникьоса в случай на
/// #                         // грешка.
/// # }
/// ```
pub fn try_div(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Divide-by-zero"))
    } else {
        Ok(a / b)
    }
}
```

## See Also

* [RFC505][RFC505] за стила на документацията;
* [API Guidelines][doc-nursery] За насоки при документиране.

[doc-nursery]: https://rust-lang-nursery.github.io/api-guidelines/documentation.html
[commonmark]: https://commonmark.org/
[RFC505]: https://github.com/rust-lang/rfcs/blob/master/text/0505-api-comment-conventions.md
[question-instead-of-unwrap]: https://rust-lang-nursery.github.io/api-guidelines/documentation.html#examples-use--not-try-not-unwrap-c-question-mark
