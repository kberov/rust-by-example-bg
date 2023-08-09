# Пропускане

Някои образци за прилагане на идеята за живот на препратките са толкова често
срещани, че проверителят на заемките разрешава да бъдат *пропуснати*, за да се
спести писане и да се подобри четимостта на кода. Това е известно като
*пропускане*[^elision] (елизия). Пропускането е разрешено само за често срещани
случаи.

Следният код показва примери на пропускане. За по-изчерпателно описание на
пропускането вижте [„Lifetime Elision”][elision] в книгата.

```rust,editable
// `elided_input` and `annotated_input` всъщност имат едно и също обявление.
// животът на `elided_input` е отгатнат от компилатора:
fn elided_input(x: &i32) {
    println!("`elided_input`: {}", x);
}

fn annotated_input<'a>(x: &'a i32) {
    println!("`annotated_input`: {}", x);
}

// Така  `elided_pass` и `annotated_pass` имат еднакви обяви.
// Животът на `elided_pass` бива добавен неявно:
fn elided_pass(x: &i32) -> &i32 { x }

fn annotated_pass<'б>(x: &'б i32) -> &'б i32 { x }

fn main() {
    let x = 3;

    elided_input(&x);
    annotated_input(&x);

    println!("`elided_pass`: {}", elided_pass(&x));
    println!("`annotated_pass`: {}", annotated_pass(&x));
}
```

Б.пр.

[^elision] пропускане, изпускане, скриване (елизия) – elision

### See also:

[elision][elision]

[elision]: https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html#lifetime-elision
