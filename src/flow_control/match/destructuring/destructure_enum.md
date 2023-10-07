# Броители

`enum` се разлага по подобен начин:

```rust,editable
// `allow` се изисква за заглушаване на предупрежденията,
//тъй като е използван само един вариант.
#[allow(dead_code)]
enum Color {
    // Тези три са определени само по име.
    Red,
    Blue,
    Green,
    // Тези обвързват разнордни поредици от стойности `u32` към различни имена:
    // цветови пространства.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // ЗАДАЧА ^ Пробвайте различни значения за `color`

    println!("Какъв е този цвят?");
    // `enum` може да бъде разложен с помощта на `match`.
    match color {
        Color::Red   => println!("Цветът е червен!"),
        Color::Blue  => println!("Цветът е син!"),
        Color::Green => println!("Цветът е зелен!"),
        Color::RGB(r, g, b) =>
            println!("Червен: {}, зелен: {} и син: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Няма нужда от други разклонения,
        // защото всички случаи вече са разгледани.
    }
}
```

### Вижте също:

[`#[allow(...)]`][allow], [Цветови модел][color_models] и [`enum`][enum]

[allow]: ../../../attribute/unused.md
[color_models]:https://bg.wikipedia.org/wiki/Цветови_модел 
[enum]: ../../../custom_types/enum.md
