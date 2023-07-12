# Менѝмост

Обвързването на променлива е непроменяемо[^immutable] по подразбирне. Но с
помощта на променителя[^modifier] `mut`, промяната на стойността може да бъде
разрешена.

```rust,editable,ignore,mdbook-runnable
fn main() {
    let _immutable_binding = 1;
    let mut mutable_binding = 1;

    println!("Before mutation: {}", mutable_binding);

    // Ok
    mutable_binding += 1;

    println!("After mutation: {}", mutable_binding);

    // Error! Cannot assign a new value to an immutable variable
    _immutable_binding += 1;
}
```
Компилаторът ще изведе подробни описания на грешките, свързани с менимостта на променливите.

[^immutable]: (не)променяемо, (не)менѝмо – (im)mutable. (Не)менѝмост, (не)променяемост – (im)mutability

[^modifier]: променител, ~модификатор~ - modifier
