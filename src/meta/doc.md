# Документация

Ползвайте `cargo doc`, за да изградите документация в `target/doc`.

Ползвайте `cargo test`, за да пуснете всички тестове (включително тези от
документацията), а `cargo test --doc` – само за документните тестове.

Тези команди ще извикат съответно `rustdoc` (и `rustc`) според необходимостта.

## Документни коментари

Документните коментари са много полезни за големи проекти. Когато пуснем
`rustdoc`, тези коментари се компилират като документация. Те се означават с
`///` и биват разбрани като [Markdown].

````rust,editable,ignore
#![crate_name = "doc"]

/// Тук се представя човешко същество.
pub struct Person {
    /// Личността трябва да има име, колкото и да го мрази Жулиета.
    name: String,
}

impl Person {
    /// Връща личност с даденото ѝ име.
    ///
    /// # Аргументи
    ///
    /// * `name` – Отрязък от низ, съдържащ името на лицето.
    ///
    /// # Примери
    ///
    /// ```
    /// // Може да имаме ограден код на Ръждьо вътре в коментарите
    /// // Ако подадем  --test на `rustdoc`, тя ще изпълни и тестовете!
    /// use doc::Person;
    /// let person = Person::new("name");
    /// ```
    pub fn new(name: &str) -> Person {
        Person {
            name: name.to_string(),
        }
    }

    /// Дава приятелски поздрав!
    ///
    /// Казва "Hello, [name](Person::name)" на лицето, с което е извикана.
    pub fn hello(& self) {
        println!("Hello, {}!", self.name);
    }
}

fn main() {
    let john = Person::new("John");

    john.hello();
}
````

За да пуснете тестовете, първо изградете кода като библиотека, после кажете
на `rustdoc` къде да намери библиотеката, така че да може да я свърже с всеки
документен тест:

```shell
$ rustc doc.rs --crate-type lib
$ rustdoc --test --extern doc="libdoc.rlib" doc.rs
```

## Документни атрибути

Следват няколко примера с най-използваните аргументи за атрибута `#[doc]`.

### `inline`

Използва се за вместване на документацията, вместо да се прави връзка към
отделна страница.

```rust,ignore
#[doc(inline)]
pub use bar::Bar;

/// документация за bar
mod bar {
    /// документацията за Bar
    pub struct Bar;
}
```

### `no_inline`

Използва се, за да предотврати свързване към отделна страница или където и да е.

```rust,ignore
// Example from libcore/prelude
#[doc(no_inline)]
pub use crate::mem::drop;
```

### `hidden`

С това казваме на `rustdoc` да не включва това в документацията:

```rust,editable,ignore
// Example from the futures-rs library
#[doc(hidden)]
pub use self::async_await::*;
```

`rustdoc` се използва широко от общността. С тази програма е създадена
[документацията за стандартната библиотека](https://doc.rust-lang.org/std/).

### Вижте също:

- [Книгата за Ръждьо: Как да правим полезни документни коментари][book]
- [Книгата за rustdoc][rustdoc-book]
- [Справочникът: Документни коментари][ref-comments]
- [RFC 1574: API Documentation Conventions][api-conv]
- [RFC 1946: Relative links to other items from doc comments (intra-rustdoc links)][intra-links]
- [Има ли някакво ръководство за коментари? (reddit)][reddit]

[markdown]: https://en.wikipedia.org/wiki/Markdown
[book]: https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html#making-useful-documentation-comments
[ref-comments]: https://doc.rust-lang.org/stable/reference/comments.html#doc-comments
[rustdoc-book]: https://doc.rust-lang.org/rustdoc/index.html
[api-conv]: https://rust-lang.github.io/rfcs/1574-more-api-documentation-conventions.html#appendix-a-full-conventions-text
[intra-links]: https://rust-lang.github.io/rfcs/1946-intra-rustdoc-links.html
[reddit]: https://www.reddit.com/r/rust/comments/ahb50s/is_there_any_documentation_style_guide_for/
