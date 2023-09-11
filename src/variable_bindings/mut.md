# Менѝмост

Обвързването на променлива е неизменяемо[^immutable] по подразбирне. Но с
помощта на изменителя[^modifier] `mut`, изменението на стойността може да бъде
разрешено.

```rust,editable,ignore,mdbook-runnable
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Преди изменението: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("След изменението: {}", mutable_binding);

    // Грешка! Не може да се присвои нова стойност на неизменяема променлива
    _immutable_binding += 1;
}
```
Компилаторът ще изведе подробни описания на грешките, свързани с менимостта на променливите.

[^immutable]: (не)менѝмо, (не)променимо – (im)mutable. (Не)менѝмост, (не)променяемост – (im)mutability

[^modifier]: изменител, ~модификатор~ - modifier
