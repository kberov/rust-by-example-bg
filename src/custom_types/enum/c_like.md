# Като в C

Броячите (`enum`) може да се ползват както в C.

```rust,editable
// Скриваме предупрежденията за неизползван код.
#![allow(dead_code)]

// `enum` с премълчан различител (започва от 0)
enum Number {
    Zero,
    One,
    Two,
}

// `enum` с изричен различител
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}

fn main() {
    // `enums` могат да бъдат ползвани (cast) като цели числа.
    println!("zero is {}", Number::Zero as i32);
    println!("one is {}", Number::One as i32);

    println!("roses are #{:06x}", Color::Red as i32);
    println!("violets are #{:06x}", Color::Blue as i32);
}
```

### Вижте също:

[casting][cast]

[cast]: ../../types/cast.md
