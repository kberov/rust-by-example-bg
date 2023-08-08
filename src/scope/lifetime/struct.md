# Структури

Отбелязването на живота на структури е също подобно на функциите:

```rust,editable
// Тип `Borrowed`, който съдържа препратка към число от тип `i32`. Препратката
// към `i32` трябва да надживее `Borrowed`.
#[derive(Debug)]
struct Borrowed<'a>(&'a i32);

// И тук двете препратки трябва да надживеят тази структура.
#[derive(Debug)]
struct NamedBorrowed<'a> {
    x: &'a i32,
    y: &'a i32,
}

// Брояч, който е `i32` или препратка към такива данни.
#[derive(Debug)]
enum Either<'a> {
    Num(i32),
    Ref(&'a i32),
}

fn main() {
    let x = 18;
    let y = 15;

    let single = Borrowed(&x);
    let double = NamedBorrowed { x: &x, y: &y };
    let reference = Either::Ref(&x);
    let number    = Either::Num(y);

    println!("x is borrowed in {:?}", single);
    println!("x and y are borrowed in {:?}", double);
    println!("x is borrowed in {:?}", reference);
    println!("y is *not* borrowed in {:?}", number);
}
```

### See also:

[`struct`s][structs]


[structs]: ../../custom_types/structs.md
