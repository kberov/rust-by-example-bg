# За разбор на грешки (Debug)

Всички типове, които искат да ползват отличители за форматиране от `std::fmt`
трябва да осъществят съответния отличител, за да бъдат печатаеми. Само типовете
от стандартната библиотека `std` осъществяват тези отличители. Всички останали
типове *трябва* някак да ги осъществят.

С помощта на отличителя `fmt::Debug` лесно се _създават негови производни_
(`derive`). *Всички* типове могат да осъществят автоматично `fmt::Debug` – да
станат призводни на `fmt::Debug`. За разлика, `fmt::Display` трябва да се
осъществи на ръка.

```rust
// Тази структура не може да бъде отпечатана нито с `fmt::Display`
// нито с `fmt::Debug`.
struct UnPrintable(i32);

// Атрибутът `derive` автоматично създава необходимото осъществяване
// (необходимия изписан код), за да направи тази структура (`struct`) печатаема с
// `fmt::Debug`.
#[derive(Debug)]
struct DebugPrintable(i32);
```

Всички типове от библиотеката `std` са печатаеми чрез означението `{:?}`:

```rust,editable
// `Structure` става производна  на `fmt::Debug`. `Structure` е структура,
// която съдържа единица данни от типа `i32`.
#[derive(Debug)]
struct Structure(i32);

// Поставяме `Structure` в структурата `Deep`, която също правим печатаема.
#[derive(Debug)]
struct Deep(Structure);

fn main() {
    // Печатането чрез `{:?}` е подобно на печатането чрез `{}`.
    println!("В една година има {:?} месеца.", 12);
    println!("{1:?} {0:?} is the {actor:?} name.",
             "Slater",
             "Christian",
             actor="actor's");

    // `Structure` е печатаема!
    println!("Сега {:?} ще се отпечати!", Structure(3));

    // Проблемът с `derive` е, че не можем да да управляваме външния вид
    // на отпечатаното. Ами ако искаме да покажем просто `7`?
    println!("Now {:?} will print!", Deep(Structure(7)));
}
```

 `fmt::Debug` определено прави типовете печатаеми, но грубо.
Ръст предоставя и „красиво отпечатване” с помощта на `{:#?}`.

```rust,editable
#[derive(Debug)]
struct Person<'a> {
    name: &'a str,
    age: u8
}

fn main() {
    let name = "Peter";
    let age = 27;
    let peter = Person { name, age };

    // Красиво отпечатване
    println!("{:#?}", peter);
}
```

 Можем ръчно да осъществим `fmt::Display`, ако искаме да управляваме показването.

### Вижте също:

[`attributes`][attributes], [`derive`][derive], [`std::fmt`][fmt],
and [`struct`][structs]

[attributes]: https://doc.rust-lang.org/reference/attributes.html
[derive]: ../../trait/derive.md
[fmt]: https://doc.rust-lang.org/std/fmt/
[structs]: ../../custom_types/structs.md

