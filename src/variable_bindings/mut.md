# Менѝмост

Обвързването на променлива е неменѝмо[^immutable] по подразбиране. Но с
помощта на променителя[^modifier] `mut`, промяната на стойността може да бъде
разрешена.

```rust,editable,ignore,mdbook-runnable
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Преди промяната: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("След промяната: {}", mutable_binding);

    // Грешка! Не може да се присвои нова стойност на неменѝма променлива
    _immutable_binding += 1;
}
```
Компилаторът ще изведе подробни описания на грешките, свързани с менимостта на променливите.

[^immutable]: (не)менѝмо, (не)променимо – (im)mutable. (Не)менѝмост, (не)променяемост – (im)mutability

[^modifier]: изменител, ~модификатор~ - modifier
