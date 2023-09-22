# while let

Подобно на `if let`, `while let` може да направи неудобните последователни
изявления `match` по-поносими. Да разгледаме следната последователност от
действия, увеличаваща `i`:

```rust
// Правим променлива `optional` от тип `Option<i32>`
let mut optional = Some(0);

// пробваме няколко пъти.
loop {
    match optional {
        // Ако `optional` е разложимо, влизаме в блока.
        Some(i) => {
            if i > 9 {
                println!("По-голямо от 9, напускаме!");
                optional = None;
            } else {
                println!("`i` е `{:?}`. Опитай отново.", i);
                optional = Some(i + 1);
            }
            // ^ Изисква 3 отстъпа!
        },
        // Напускаме повторенията, ако разлагането не успее:
        _ => { break; }
        // ^ Защо това да е задължително? Трябва да има по-добър начин!
    }
}
```

Ако използваме `while let`, нещата изглеждат по-добре:

```rust,editable
fn main() {
    // Правим променлива `optional` от тип `Option<i32>`
    let mut optional = Some(0);

    // Това се чете: "докато `let` разлага `optional` в
    // `Some(i)`, изпълняваме блока (`{…}`). Иначе прекъсваме (`{ break; }`).
    while let Some(i) = optional {
        if i > 9 {
            println!("По-голямо от 9, напускаме!");
            optional = None;
        } else {
            println!("`i` е `{:?}`. Опитай отново.", i);
            optional = Some(i + 1);
        }
        // ^ По-малко отместване вдясно и няма изрично изискване
        // за обработка на неуспешен случай.
    }
    // ^ `if let` – имаше допълнителни условия `else`/`else if`
    // `while let` – няма такива.
}
```

### See also:

[`enum`][enum], [`Option`][option] и [RFC][while_let_rfc]

[enum]: ../custom_types/enum.md
[option]: ../std/option.md
[while_let_rfc]: https://github.com/rust-lang/rfcs/pull/214
