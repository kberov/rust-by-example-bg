# Множество предели

Повече предели за отделен тип може да се приложат чрез знака `+`.
Обикновено различните типове се отделят със `,`.

```rust,editable
use std::fmt::{Debug, Display};

fn compare_prints<T: Debug + Display>(t: &T) {
    println!("Debug: `{:?}`", t);
    println!("Display: `{}`", t);
}

fn compare_types<T: Debug, U: Debug>(t: &T, u: &U) {
    println!("t: `{:?}`", t);
    println!("u: `{:?}`", u);
}

fn main() {
    let string = "words";
    let array = [1, 2, 3];
    let vec = vec![1, 2, 3];

    compare_prints(&string);
    //compare_prints(&array);
    // ^ ЗАДАЧА: Разкоментирайте горния ред.

    compare_types(&array, &vec);
}
```

### Вижте също:

[`std::fmt`][fmt] и [`отличители`][traits]

[fmt]: ../hello/print.md
[traits]: ../trait.md
