# Броячи

`enum` се разлага по подобен начин:

```rust,editable
// `allow` required to silence warnings because only
// one variant is used.
#[allow(dead_code)]
enum Color {
    // Тези три са определени само по име.
    Red,
    Blue,
    Green,
    // Тези обвързват разнордни списъци от стойности `u32` към различни имена:
    // цветови пространства.
    RGB(u32, u32, u32),
    HSV(u32, u32, u32),
    HSL(u32, u32, u32),
    CMY(u32, u32, u32),
    CMYK(u32, u32, u32, u32),
}

fn main() {
    let color = Color::RGB(122, 17, 40);
    // TODO ^ Try different variants for `color`

    println!("What color is it?");
    // An `enum` can be destructured using a `match`.
    match color {
        Color::Red   => println!("The color is Red!"),
        Color::Blue  => println!("The color is Blue!"),
        Color::Green => println!("The color is Green!"),
        Color::RGB(r, g, b) =>
            println!("Red: {}, green: {}, and blue: {}!", r, g, b),
        Color::HSV(h, s, v) =>
            println!("Hue: {}, saturation: {}, value: {}!", h, s, v),
        Color::HSL(h, s, l) =>
            println!("Hue: {}, saturation: {}, lightness: {}!", h, s, l),
        Color::CMY(c, m, y) =>
            println!("Cyan: {}, magenta: {}, yellow: {}!", c, m, y),
        Color::CMYK(c, m, y, k) =>
            println!("Cyan: {}, magenta: {}, yellow: {}, key (black): {}!",
                c, m, y, k),
        // Don't need another arm because all variants have been examined
    }
}
```

### Вижте също:

[`#[allow(...)]`][allow], [Цветови модел][color_models] и [`enum`][enum]

[allow]: ../../../attribute/unused.md
[color_models]:https://bg.wikipedia.org/wiki/Цветови_модел 
[enum]: ../../../custom_types/enum.md
