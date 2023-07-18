# Обвързване

При косвения достъп до променливи (съответно сравнение чрез буквални стойности)
е невъзможно в разклонението да се използва тази променлива (или стойност)
без обвързване наново. `match` предоставя знака `@` за обвързване на стойности
с имена:

```rust,editable
// Функция `age`, връщаща стойност от тип `u32`.
fn age() -> u32 {
    15
}

fn main() {
    println!("Tell me what type of person you are");

    match age() {
        0             => println!("I haven't celebrated my first birthday yet"),
        // можехме направо да сравним 1 ..= 12, но тогава на каква възраст би
        // било едно новородонео? Затова, обвързваме `n` с последователността
        // 1 ..= 12. Сега можем да кажем каква е възрастта.
        n @ 1  ..= 12 => println!("I'm a child of age {:?}", n),
        n @ 13 ..= 19 => println!("I'm a teen of age {:?}", n),
        // Няма обвързване. Връщаме стойността.
        n             => println!("I'm an old person of age {:?}", n),
    }
}
```

Можете да ползвате обвързване за разлагане на варианти на броячи, например `Option`:

```rust,editable
fn some_number() -> Option<u32> {
    Some(42)
}

fn main() {
    match some_number() {
        // Got `Some` variant, match if its value, bound to `n`,
        // is equal to 42.
        Some(n @ 42) => println!("The Answer: {}!", n),
        // Match any other number.
        Some(n)      => println!("Not interesting... {}", n),
        // Match anything else (`None` variant).
        _            => (),
    }
}
```

### See also:
[функции][functions], [броячи][enums] and [`Option`][option]

[functions]: ../../fn.md
[enums]: ../../custom_types/enum.md
[option]: ../../std/option.md
