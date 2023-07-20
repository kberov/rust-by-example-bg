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
                println!("Greater than 9, quit!");
                optional = None;
            } else {
                println!("`i` is `{:?}`. Try again.", i);
                optional = Some(i + 1);
            }
            // ^ Изисква 3 отстъпа!
        },
        // Напускаме повторенията ако разлагането не успее:
        _ => { break; }
        // ^ Защо това е задължително? Трябва д аима по-добър начин!
    }
}
```

Ако използваме `while let`, нещата изглеждат по-добре:

```rust,editable
fn main() {
    // Правим променлива `optional` от тип `Option<i32>`
    let mut optional = Some(0);

    // This reads: "while `let` destructures `optional` into
    // `Some(i)`, evaluate the block (`{}`). Else `break`.
    while let Some(i) = optional {
        if i > 9 {
            println!("Greater than 9, quit!");
            optional = None;
        } else {
            println!("`i` is `{:?}`. Try again.", i);
            optional = Some(i + 1);
        }
        // ^ Less rightward drift and doesn't require
        // explicitly handling the failing case.
    }
    // ^ `if let` имаше допълнителни условия `else`/`else if`
    // `while let` няма такива.
}
```

### See also:

[`enum`][enum], [`Option`][option], and the [RFC][while_let_rfc]

[enum]: ../custom_types/enum.md
[option]: ../std/option.md
[while_let_rfc]: https://github.com/rust-lang/rfcs/pull/214
