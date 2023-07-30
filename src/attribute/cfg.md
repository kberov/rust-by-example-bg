# `cfg`

Условни проверки на настройките на компилиране са възможни чрез два оператора:

* атрибутът `cfg`: `#[cfg(...)]`
* Макрото `cfg!`: `cfg!(...)` при условни изрази

While the former enables conditional compilation, the latter conditionally
evaluates to `true` or `false` literals allowing for checks at run-time. Both
utilize identical argument syntax.

За разлика от `#[cfg]`, `cfg!` не премахва код, а само се оценява като „да” или
„не”. Вички блокове в израз if/else трябва да работят, когато се ползва  `cfg!`,
независимо как ще се оцени `cfg!`.

```rust,editable
// Тази функция се компилира само ако целевата ОУ е Линукс
#[cfg(target_os = "linux")]
fn are_you_on_linux() {
    println!("You are running linux!");
}

// А тази функция се компилира само ако целевата ОУ *не е* Линукс
#[cfg(not(target_os = "linux"))]
fn are_you_on_linux() {
    println!("You are *not* running linux!");
}

fn main() {
    are_you_on_linux();

    println!("Are you sure?");
    if cfg!(target_os = "linux") {
        println!("Yes. It's definitely linux!");
    } else {
        println!("Yes. It's definitely *not* linux!");
    }
}
```

### See also:

[the reference][ref], [`cfg!`][cfg], and [macros][macros].

[cfg]: https://doc.rust-lang.org/std/macro.cfg!.html
[macros]: ../macros.md
[ref]: https://doc.rust-lang.org/reference/attributes.html#conditional-compilation
