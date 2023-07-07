# Форматиране 

Видяхме, че форматирането се указва чрез *низ за формат*:

* `format!("{}", foo)` -> `"3735928559"`
* `format!("0x{:X}", foo)` -> [`"0xDEADBEEF"`][deadbeef]
* `format!("0o{:o}", foo)` -> `"0o33653337357"`

Една и съща променлива (`foo`) може да бъде форматирана по различен начин в
зависимост от това какъв *тип аргумент* се използва `X`, `o` или *неустановен*.

Тази функционалност за форматиране се осъществява чрез отличители и за всеки
тип аргумент се осъществява различен отличител. Най-често използваният
отличител за форматиране е `Display`. `Display` обработва случаите, когато типът
на аргумента не е указан, например `{}`.

```rust,editable
use std::fmt::{self, Formatter, Display};

struct City {
    name: &'static str,
    // Географска ширина
    lat: f32,
    // Географска дължина
    lon: f32,
}

impl Display for City {
    // `f` е склад¹ и този метод трябва да пише форматирания низ в него.
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
        let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

        // макросът `write!` е като `format!`, но той пише форматирания низ
        // в склад (първия аргумент – `f`).
        write!(f, "{}: {:.3}°{} {:.3}°{}",
               self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
    }
}

#[derive(Debug)]
struct Color {
    red: u8,
    green: u8,
    blue: u8,
}

fn main() {
    for city in [
        City { name: "Dublin", lat: 53.347778, lon: -6.259722 },
        City { name: "Oslo", lat: 59.95, lon: 10.75 },
        City { name: "Vancouver", lat: 49.25, lon: -123.1 },
    ] {
        println!("{}", city);
    }
    for color in [
        Color { red: 128, green: 255, blue: 90 },
        Color { red: 0, green: 3, blue: 254 },
        Color { red: 0, green: 0, blue: 0 },
    ] {
        // Променете кода да използва {},
        // след като осъществите fmt::Display за Color.
        println!("{:?}", color);
    }
}
```

Можете да видите [пълния списък отличители за форматиране][fmt_traits] и
техните типове аргументи в документацията за [`std::fmt`][fmt].

### Упражнение

Осъществете `fmt::Display` за труктурата `Color`, така че изходът да изглежда
така:

```text
RGB (128, 255, 90) 0x80FF5A
RGB (0, 3, 254) 0x0003FE
RGB (0, 0, 0) 0x000000
```

Три съвета в случай на затруднения:

* Формулата за изчисляване на цветове в цветовото пространство ЧЗС е както
  следва: `RGB = (R*65536)+(G*256)+B , (където R е червено (RED), G е зелено
  (GREEN) и B е синьо (BLUE))`. Повече за ЧЗС и изчислението
  на цветовете: [RGB Color Codes Chart][rgb_color].
* Може да искате [да покажете всеки цвят повече от веднъж][named_parameters].
* Можете да [създадете отстъп с две нули][fmt_width] с `:0>2`.

### Вижте също:

[`std::fmt`][fmt]

[^buffer]: склад – buffer

[rgb_color]: https://www.rapidtables.com/web/color/RGB_Color.html#rgb-format
[named_parameters]: https://doc.rust-lang.org/std/fmt/#named-parameters
[deadbeef]: https://en.wikipedia.org/wiki/Deadbeef#Magic_debug_values
[fmt]: https://doc.rust-lang.org/std/fmt/
[fmt_traits]: https://doc.rust-lang.org/std/fmt/#formatting-traits
[fmt_width]: https://doc.rust-lang.org/std/fmt/#width
