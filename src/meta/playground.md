# Игрална площадка 

[Игралната площадка на Ръждьо][rust-playground] е място за опити с код на
Ръждьо през уеб четец.

## Ползване с `mdbook`

В [`mdbook`][mdbook] можем да направим примерите да могат да се изпълняват и
редактират.

```rust,editable
fn main() {
    println!("Hello World!");
}
```

Това позволява читателят не само да чете примерите с код , но и да ги променя.
Същественото тук е да добавим думата `editable` към оградения ни блок код,
разделен от предната дума със запетая.

````markdown
```rust,editable
//...place your code here
```
````

В допълнение можем да добавим `ignore`, ако искаме `mdbook` да пропусне този
код, докато изгражда и тества документацията като книга.

````markdown
```rust,editable,ignore
//...place your code here
```
````

## Ползване с документация

Може би сте забелязали някъде в [представителната документация на
Ръждьо][official-rust-docs] бутона „Run”, който отваря примера с код в нов
раздел в Игралната площадка на Ръждьо. Тази способност се включва, ако ползваме
атрибута `#[doc]`, наречен [`html_playground_url`][html-playground-url].

### See also:

- [Игралната площадка на Ръждьо][rust-playground]
- [rust-playground][rust-playground]
- [Книгата за rustdoc][rustdoc-book]

[rust-playground]: https://play.rust-lang.org/
[rust-playground]: https://github.com/integer32llc/rust-playground/
[mdbook]: https://github.com/rust-lang/mdBook
[official-rust-docs]: https://doc.rust-lang.org/core/
[rustdoc-book]: https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html
[html-playground-url]: https://doc.rust-lang.org/rustdoc/the-doc-attribute.html#html_playground_url
